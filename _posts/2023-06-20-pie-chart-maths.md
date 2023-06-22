---
layout: post
current: post
cover: assets/images/covers/pie.jpg
navigation: True
title: Designing a Pie Chart
date: 2023-06-20 11:00:00
# tags: reactNative maths
class: post-template
subclass: "post tag-react-native tag-maths"
---

Sometimes, a small change in design can make a big difference to the time it takes to implement. The happy path is only one possibility, and considering how a design will look in different scenarios is an important factor in ensuring the component will behave as intended. In this post, we explore some difficulties of designing and implementing a pie chart.

Our team wanted a visualisation to show the proportion of medications taken to schedule, and decided a pie chart was the best option. Our designer came up with an option that looked relatively simple. If the percentage was large enough, it would be written in the relevant segment; otherwise we would place the label outside the chart and point to the segment. Something like this:

![A pie chart with neat looking labels](assets/images/posts/pie-design.png)

The first problem with this is, what does _large enough_ mean? Even if we keep the size of the font fixed relative to the size of the graph, whether or not the label fits inside a segment depends on where that segment is. Segments that sit horizontally in the chart will be able to fit longer text than vertical segments.

![A pie chart with two segments each representing ten percent, one where the label fits inside and one where it doesn't](assets/images/posts/pie-large-enough.png)

We settled on applying internal labels to segments whose value was 10% or more. This was large enough that the percentage label would fit any orientation, including the vertical segments. Another question was where to place the label: putting it at the midway point between the circle's center and edge looked somehow off, so we used this neat calculation to place it at the segment's centre of gravity if possible, whilst enforcing a minimum distance from the circle's centre:

```typescript
const centreOfGravityRadius = (2 * r * Math.sin(theta)) / (3 * theta);
const labelRadius = Math.max(centreOfGravityRadius, r * 0.4);

const x = labelRadius * Math.cos(phi);
const y = labelRadius * Math.sin(phi);
```

The angles for the calculation are illustrated on the diagram below, where phi is φ and theta is θ. The radius of the circle is `r`, and the variable `labelRadius` gives the distance between the centre of the circle and the point P.

![A diagram labelling theta, half the internal angle of the segment; and phi, the angle from 0 degrees to halfway through the segment](assets/images/posts/pie-angles.png)

The next problem was how to arrange the external labels. The design we were using had a line that went horizontally from the segment, then changed direction and pointed to the label. This raised a lot of questions - in which direction should the line bend? How far should it extend? Our initial approach was to have it extend a fixed distance and direction, where the direction is determined by which quadrant the segment is in. This looked good in most cases, but could result in overlapping labels if two small segments were in the same quadrant.

![A pie chart with two small segments whose labels overlap](assets/images/posts/pie-bend-initial.png)

Next, we tried making the line bend towards whichever adjacent segment was larger. This meant that when two small segments were next to each other, the labels would not overlap.

![A pie chart with two small segments whose labels do not overlap](assets/images/posts/pie-bend-to-largest.png)

However, if there were three small segments in a row, the middle segment would still overlap with one of them.

![A pie chart with three small segments whose labels overlap](assets/images/posts/pie-bend-many-small-segments.png)

Although the case of three small segments could be fixed by setting the middle segment to have a straight line instead of a bendy one, this solution would not generalise if we were to have four small segments in a row (which could be possible, given that we had five possible responses). The bendy lines had another problem too: colour contrast. If a dark line goes into a dark segment it gets lost, and if a white line is needed it gets lost outside the pie chart.

![A pie chart with labels that are hard to see due to poor colour contrast](assets/images/posts/pie-colour-contrast.png)

Focusing on the contrast issue, our designer proposed a few solutions. What if we gave the white line a shadow? What if the dark line changes colour when it strays into a dark segment?

![A pie chart with good contrast, but looking cluttered](assets/images/posts/pie-colour-contrast-proposed-solutions.png)

Although it was technically possible to change the line colour according to suit its background, it was complex and one might argue whether the effort was worth the final result. Not only that, but we still hadn't figured out how to animate the labels. You see, we wanted the chart to be able to transition smoothly between one month's data and the next. We anticipated labels moving from place to place, inside to outside the chart, and sometimes disappearing altogether. We were struggling to make the static labels work, and making animated ones would be much more difficult.

In the end, we decided to remove the percentage labels from the chart. Instead, we placed them in the key alongside the descriptions for each status. This new design was much easier to implement, and can easily be generalised without needing to worry about edge cases.

![A pie chart with percentage labels in the key](assets/images/posts/pie-design-final.png)

The original pie chart was built in React Native, but I have since written a simple web app with similar functionality. [You can see the repo, 'Graph Maven', here](https://github.com/jenniferanneaus/graph-maven).
