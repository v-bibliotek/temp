# Issues with ReferenceManyToManyInput

This may be issues related to my implementation rather than the component itself.

## SelectArrayInput not populated on first visit to editform of item

Screen recording showing how one has to revisit the edit form in order for the SelectArrayInput to be properly populated.

https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202020-12-12%20at%2020.30.53.mov

## Prop perPage in ReferenceManyToManyInput

The perPage prop should be honoured in the ReferenceManyToManyInput as in the ReferenceManyToManyField. Currently it is limited to load 25 relations. Hence, not all options can be displayed properly:

https://github.com/v-bibliotek/temp/blob/master/ReferenceManyToManyInput%20-%2025%20limit%2C%20perPage%20not%20honoured.mov

## useReferenceManyToManyUpdate

### Multiple many-to-many-related resources?
Can an edit form only save one many-to-many-relation using the hook `useReferenceManyToManyUpdate`? In other words as developer I have to choose what the user is able to edit a form, either _subject terms_, _authors_, _age categories_ or _genres_? 

Would supplying an array of relations to the hook be a solution?

### Saving requires change in source-resource

Assume I edit a resource, say the _books_-resource. The resource has a many-to-many-relations with the _subjects_-resource. When pressing save the data has to contain changes of the _books_-resource. If data only contains changes of the related resource, the _subjects_-resource, data will not be saved. 


