# Issues with ReferenceManyToManyInput

## SelectArrayInput not populated on first visit to editform of item

Screen recording showing how one has to revisit the edit form in order for the SelectArrayInput to be properly populated.

https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202020-12-12%20at%2020.30.53.mov

## Prop perPage in ReferenceManyToManyInput

The perPage prop should be honoured in the ReferenceManyToManyInput as in the ReferenceManyToManyField. Currently it is limited to load 25 relations, not honouring perPage prop. Hence, not all options can be displayed properly:

https://github.com/v-bibliotek/temp/blob/master/ReferenceManyToManyInput%20-%2025%20limit%2C%20perPage%20not%20honoured.mov

## useReferenceManyToManyUpdate

Can an edit form only save one many-to-many-relation using the hook `useReferenceManyToManyUpdate`, ie as developer I have to choose what the user is abel to update, either  add subject terms, authors, age categories or genres? 

Would supplying an array of relations to the hook be a solution?


