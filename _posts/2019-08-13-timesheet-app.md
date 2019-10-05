---
layout: post
current: post
cover:  assets/images/timesheet.jpg
navigation: True
title: Timesheet App
date: 2019-08-13 18:00:00
tags: [ruby, rails]
class: post-template
subclass: 'post tag-ruby tag-rails'
---

Last week I created a [timesheet application](https://github.com/jenniferanne1991/timesheet_app) using Ruby on Rails. The app is basic, but working through it provided valuable opportunities to learn more about testing, validation and other Rails essentials.

The task was to create a timesheet entry application consisting of two pages. The first page allows users to create a new timesheet, and the other lists all stored timesheets. When users try to create a new timesheet, the app should check that the entry contains valid information before saving it. A timesheet entry is considered "valid" if it fulfils the following requirements:
+ It must not overlap with any existing timesheet entries.
+ Its date must not be in the future.
+ The finish time must be after the start time.
+ A valid date, start time and finish time must be given.

Based on the information entered by the user, the app calculates a dollar value for each timesheet. The value is calculated according to the following rules:
+ On Mondays, Wednesdays and Fridays, the rate is:
    + $22/hour between 7am and 7pm (call these daytime hours),
    + $33/hour during other times of day (call these off-peak hours).
+ On Tuesdays and Thursdays, the rate is:
    + $25/hour between 5am and 5pm (daytime hours),
    + $35/hour during other times of day (off-peak hours).
+ On weekends, the rate is always $47/hour.

After creating a basic Rails app with a Timesheets controller, we begin by creating a function to calculate the value of a timesheet. For timesheets on weekdays, we will need to determine how many hours are worked during the daytime, and how many are off-peak. That is, we will find the intersection between the following time intervals: 
+ ```a```, the time interval worked, and 
+ ```b```, the time interval 7am-7pm or 5am-5pm, depending on the day.

The number of hours worked during the daytime is the intersection between ```a``` and ```b```, which can be calculated using the following code:

```ruby
# parameters start_a, finish_a, start_b and finish_b are 'Time' objects
def time_intersection(start_a, finish_a, start_b, finish_b)
   if finish_a < start_b or finish_b < start_a
      # If one interval finishes before the other starts, the time intervals do not overlap.
      return 0
   else
      # Otherwise, intersection goes from the maximum start time to minimum finish time.
      return ([finish_a, finish_b].min - [start_a, start_b].max) / 1.hours
   end
end
```

To determine the dollar value, we can then multiply each time interval by the appropriate rate, splitting weekday hours into daytime and off-peak as necessary. 

To determine which day of the week the timesheet represents, Ruby's `Date` class has built in methods that make it easy for us. Using `monday?`, for example, will return true if the given date is a Monday, and analogous methods exist for all other days of the week. So then, our calculations to determine dollar value are as follows:

```ruby
# date here should be a 'Date' object, and start and finish should be 'Time' objects
def timesheet_value(date, start, finish)
   start_time = Time.parse(start.strftime('%I:%M%P'))
   finish_time = Time.parse(finish.strftime('%I:%M%P'))
   dollar_value = 0
   if date.saturday? or date.sunday?
      dollar_value = 47 * (finish_time - start_time) / 1.hours
   elsif date.tuesday? or date.thursday?
      five_am = Time.parse("5:00am")
      five_pm = Time.parse("5:00pm")
      # Calculate time between 5am and 5pm, and 'off-peak' time
      total_work_time = (finish_time - start_time) / 1.hours
      five_am_to_five_pm_time = time_intersection(start_time, finish_time, five_am, five_pm)
      off_peak_time = total_work_time - five_am_to_five_pm_time
      dollar_value = 25 * five_am_to_five_pm_time + 35 * off_peak_time
   else
      seven_am = Time.parse("7:00am")
      seven_pm = Time.parse("7:00pm")
      # Calculate time between 7am and 7pm, and 'off-peak' time
      total_work_time = (finish_time - start_time) / 1.hours
      seven_am_to_seven_pm_time = time_intersection(start_time, finish_time, seven_am, seven_pm)
      off_peak_time = total_work_time - seven_am_to_seven_pm_time
      dollar_value = 22 * seven_am_to_seven_pm_time + 33 * off_peak_time
   end
   return dollar_value
end
``` 

These methods are both placed in the helper file, which must be included in the relevant controller. In this case, we have a controller called `timesheets_controller` so our helper file is `timesheets_helper.rb`. 

We next turn our attention to our `timesheet` model, creating timesheets using a form and how to validate user input. I used a form based on the one Michael Hartl describes in his [Rails Tutorial](https://www.railstutorial.org/book). However, note that the tutorial uses `form_for`, which is now soft-deprecated. We will use the `form_with` helper instead:

```erb
<div class="row">
  <%= form_with(model: @timesheet, url: new_path) do |f| %>
    <h2>Create new timesheet</h2>
    <%= render 'shared/error_messages' %>
    <%= f.label :date %>
    <br>
    <%= f.date_field :date, class: 'form-control' %>
    <br padding-bottom: 10px>
    <%= f.label :start %>
    <br>
    <%= f.time_field :start, class: 'form-control' %>
    <br padding-bottom: 10px>
    <%= f.label :finish %>
    <br>
    <%= f.time_field :finish, class: 'form-control' %>
    <br padding-bottom: 10px>
    <%= f.submit "Submit timesheet", class: "button" %>
  <% end %>
</div>
```

Since Turbolinks does not support `render`, you can use the `turbolinks_render` gem to ensure the error messages display correctly. To retrieve the error messages, the code in `app/views/shared/_error_messages.html.erb`, is as follows:

```erb
<% if @timesheet.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      The form contains <%= pluralize(@timesheet.errors.count, "error") %>.
    </div>
    <ul>
    <% @timesheet.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

You might have noticed from the above code that Rails has a nice helper called `pluralize`; this takes a number and a singular noun, and will pluralise the noun unless the number is 1. Using this, we can avoid having gramatically incorrect sentences without having to write our own `if` statement.

Now, to make sure appropriate error messages are generated, we will write validations. Ruby has already created the file `app/models/timesheet.rb` for us, and this is where we will add our validations. `Timesheet` inherits from `Active Record`, which includes many pre-defined helpers such as `presence`, which we will use now:

```ruby
validates :start, presence: true
validates :finish, presence: true
validates :date, presence: true
```

For our other conditions, we can write custom validation methods. In addition to writing the methods, we must register each of them using the ```validate``` class method. So, to ensure that the user has specified a date and times that are valid, we include the following code in `timesheet.rb`:

```ruby
validate :date_cannot_be_in_future
validate :start_must_be_before_finish
validate :timesheet_entries_must_not_overlap

def date_cannot_be_in_future
  if date.present? && date > Date.today
    errors.add(:date, "can't be in the future")
  end
end

def start_must_be_before_finish
  if start.present? && finish.present? && start >= finish
    errors.add(:finish, "time must be after start time")
  end
end

def timesheet_entries_must_not_overlap
  same_date_timesheets = Timesheet.where(date: date).where.not(id: self.id)
  if same_date_timesheets == nil
    return
  end
  same_date_timesheets.each do |same_date_timesheet|
    unless same_date_timesheet.finish < start or finish < same_date_timesheet.start
      unless errors.added?(:This, "entry overlaps with an exiting timesheet entry")
        errors.add(:This, "entry overlaps with an exiting timesheet entry")
      end
    end
  end
end
```

Notice that `Timesheet` can be used to access all the timesheets that have been saved so far, and we can use `where.not(id: self.id)` to ensure that we don't include the current timesheet in our validation. For example, after calculating the value of a timesheet we will need to re-save it. However, if we don't exclude the current timesheet from our validation, we will be unable to update it because its time frame overlaps with itself.

Just as important as writing validations is writing tests to accompany them. The idea of automated tests is to assert that the program behaves correctly, without having to manually test the same things over again after you update your code. Unit tests for our timesheet reside in `test/models/timesheet_test.rb`. At the beginning of the class, we will define a setup method that includes valid input for a timesheet:

```ruby
def setup
  @timesheet = Timesheet.new(date: Date.new(2019,8,8), start: Time.new(2019,8,8,12,00), finish: Time.new(2019,8,8,19,30))
end
```

We can then include a test to make sure that the default information results in a valid timesheet being created.

```ruby
test "should be valid" do
  assert @timesheet.valid?
end
```

To run tests, execute `rails test` or `rails t` from the app's root directory in your console. The string between `test` and `do` is what will be displayed if the test fails. For example, if `Timesheet` decides that your default timesheet is not valid, you will get an error message along the lines of: 
<p style="text-align: center; font-family: monospace, monospace;font-size: 0.85em; background-color: #e9f1f5">timesheet_test#test_should_be_valid: Expected false to be truthy</p>

This error message will, of course, differ depending on your environment. After the `should be valid` test, our tests will involve making a timesheet invalid in a single way, and asserting that the timesheet is not valid. For example, we might remove a piece of information that is required or create a timesheet whose date is in the future. By reading the tests below, you should get an idea of what each test does:

```ruby
test "date must be present" do
  @timesheet.date = nil
  assert_not @timesheet.valid?
end

test "start time must be present" do
  @timesheet.start = nil
  assert_not @timesheet.valid?
end

test "finish time must be present" do
  @timesheet.finish = nil
  assert_not @timesheet.valid?
end

test "finish time must be after start" do
  @timesheet.start = Time.now
  @timesheet.finish = Time.now - 1800
  assert_not @timesheet.valid?
end

test "date cannot be in future" do
  @timesheet.date = Date.today + 2.days
  assert_not @timesheet.valid?
end
```

Whilst these tests help us with simple errors, they still do not check for overlapping timesheet entries. They also do not consider timesheet pay calculations, or whether our pages are loading correctly. For that we will use integration tests; to get started we will execute
<p style="text-align: center; font-family: monospace, monospace;font-size: 0.85em; background-color: #e9f1f5">rails generate integration_test timesheets_new</p>
in our console. If `generate` is too long, you can replace it with just `g`. After running this command, Rails will generate the file `timesheets_new_test.rb` in `test/integration` for us.

First, we will write a test to check that invalid timesheets are not saved. In addition to asserting no timesheet is saved when invalid information is given, we will also assert that the "Create Timesheet" page is re-rendered, with error messages displayed and fields with errors highlighted:

```ruby
test "invalid timesheet information" do
  get new_path
  assert_no_difference 'Timesheet.count' do
    post timesheets_path, params: { timesheet: {
      date: Date.tomorrow,
      start: Time.now + 1800,
      finish: Time.now - 1800}   }
  end
  assert_template 'timesheets/new'
  assert_select 'div#error_explanation'
  assert_select 'div.field_with_errors'
end
```

This introduces us to the main tools we will use in our integration tests: telling Rails we are interested in the "Create Timesheet" page using ```get new_path```, then asserting whether or not there is a difference in count and asserting the correct template is rendered. The other integration tests we will include correspond to timesheet pay calculations and overlapping timesheet entries. Again, the code is fairly straighforward to read:

```ruby
test "overlapping timesheet entries" do
  Timesheet.create(date: Date.new(2000,1,1), 
    start: Time.httpdate("Sat, 01 Jan 2000 09:00:00 GMT"), 
    finish: Time.httpdate("Sat, 01 Jan 2000 17:00:00 GMT"))
  get new_path
  # Check that if an overlapping timesheet entry is submitted, it does not save.
  assert_no_difference 'Timesheet.count' do
    post timesheets_path, params: { timesheet: {
      date: Date.new(2000,1,1),
      start: Time.httpdate("Sat, 01 Jan 2000 07:23:00 GMT"),
      finish: Time.httpdate("Sat, 01 Jan 2000 10:47:00 GMT")}   }
  end
  # Ensure 'new' page is re-rendered and error message is displayed.
  assert_template 'timesheets/new'
  assert_select 'div#error_explanation'
end

test "valid timesheet information saturday" do
  get new_path
  assert_difference 'Timesheet.count', 1 do
    post timesheets_path, params: { timesheet:{
      date: Date.new(2000,1,1),
      start: Time.httpdate("Sat, 01 Jan 2000 03:00:00 GMT"),
      finish: Time.httpdate("Sat, 01 Jan 2000 13:30:00 GMT")} }
  end
  # Check that the app redirects to index and correctly calculates pay.
  follow_redirect!
  assert_template 'timesheets/index'
  assert Timesheet.last.pay == 493.50
end

test "valid timesheet information thursday" do
  get new_path
  assert_difference 'Timesheet.count', 1 do
    post timesheets_path, params: { timesheet:{
      date: Date.new(2019,8,8),
      start: Time.httpdate("Thu, 08 Aug 2019 12:00:00 GMT"),
      finish: Time.httpdate("Thu, 08 Aug 2019 20:15:00 GMT")} }
  end
  # Check that the app redirects to index and correctly calculates pay.
  follow_redirect!
  assert_template 'timesheets/index'
  assert Timesheet.last.pay == 238.75
end

test "valid timesheet information wednesday across peak" do
  get new_path
  assert_difference 'Timesheet.count', 1 do
    post timesheets_path, params: { timesheet:{
      date: Date.new(2019,8,7),
      start: Time.httpdate("Wed, 07 Aug 2019 04:00:00 GMT"),
      finish: Time.httpdate("Wed, 07 Aug 2019 21:30:00 GMT")} }
  end
  # Check that the app redirects to index and correctly calculates pay.
  follow_redirect!
  assert_template 'timesheets/index'
  assert Timesheet.last.pay == 445.5
end

test "valid timesheet information wednesday outside peak" do
  get new_path
  assert_difference 'Timesheet.count', 1 do
    post timesheets_path, params: { timesheet:{
      date: Date.new(2019,8,7),
      start: Time.httpdate("Wed, 07 Aug 2019 05:00:00 GMT"),
      finish: Time.httpdate("Wed, 07 Aug 2019 06:30:00 GMT")} }
  end
  # Check that the app redirects to index and correctly calculates pay.
  follow_redirect!
  assert_template 'timesheets/index'
  assert Timesheet.last.pay == 49.5
end
```

Tests are extremely useful in order to check that your app behaves as intended, and to catch any regressions in your code. Of course, in order to catch these regressions, your tests need to cover your code effectively. 

This is where code coverage analysis tools such as [SimpleCov](https://github.com/colszowka/simplecov) come in. These will analyse your code for you, telling you which lines of code are covered by your automated tests. I used [Andy Croll's tutorial](https://andycroll.com/ruby/use-simplecov/) to get SimpleCov up and running, and to make sure it ignored the appropriate files. It is worth mentioning that you don't necessarily need to aim for 100% code coverage, and having 100% coverage doesn't mean that your program will be error-free. However, it is still useful to see which parts of your code aren't covered by automated testing, and consider whether you should write extra tests.

With the basic timesheet app up and running, this week's project is complete. There are obviously many more features that could be added to this app; for example, the ability to edit or delete timesheets, or create users to whom the timesheets could belong. We also haven't looked at bootstrap and css, which were both used (in moderation) to make this app look a little bit nicer. Nevertheless, it introduces a few useful Rails concepts that I hadn't used before this project.