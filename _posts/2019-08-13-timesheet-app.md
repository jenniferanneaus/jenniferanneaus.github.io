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

To calculate the dollar value on weekdays, we need to determine how many hours are worked during the daytime, and how many are off-peak. For this, we first find the intersection between the following time intervals: 
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

To determine the dollar value, we can then multiply each time interval by the appropriate rate, splitting weekday hours into daytime and off-peak as necessary. To determine which day of the week the timesheet represents, Ruby's `Date` class has built in methods that make it easy for us. Using `monday?` will return true if the given date is a Monday, and analogous methods exist for all other days of the week. So, then, our calculations to determine dollar value are as follows:

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

These methods are both placed in the helper file (```timesheets_helper.rb``` in my case), and the helper is included in the relevant controller. We next turn our attention to building a form to create timesheet entries, and validating user input.

+ forms and stuff
+ simple validations and tests
+ custom validations and tests

Tests are extremely useful in order to check that your app behaves as intended, and to catch any regressions in your code. Of course, in order to catch these regressions, your tests need to cover your code effectively. This is where code coverage analysis tools such as [SimpleCov](https://github.com/colszowka/simplecov) come in. These will analyse your code for you, telling you which lines of code are covered by your automated tests. I used [Andy Croll's tutorial](https://andycroll.com/ruby/use-simplecov/) to get SimpleCov up and running, and make sure it ignored the appropriate files. It is worth mentioning that you don't necessarily need to aim for 100% code coverage, and having 100% coverage doesn't mean that your program will be error-free. However, it is still useful to see which parts of your code aren't covered by automated testing, and consider whether you should write extra tests.

With the basic timesheet app up and running, this week's project is complete. There are obviously many more features that could be added to this app; for example, the ability to edit or delete timesheets, or create users to whom the timesheets could belong. We also haven't looked at bootstrap and css, which were both used to make this app look a little bit nicer. Nevertheless, it introduces a few handy Rails concepts that I hadn't used before this project.