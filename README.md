# Issues with ReferenceManyToManyInput (ra-relationship v2)

This may be issues related to my implementation rather than in the component itself.

Assume that the many-to-many-relation between resources is as follows:

```
titles <-> titles_subjects <-> subjects
titles <-> titles_contributors <-> contributors
titles <-> titles_attributes <-> attributes
```
So the editing form uses _titles_ as resource and _subjects_, _contributors_ and _attributes_ are referenced data.  

For example _subjects_ are to be selected from an SelectArrayInput or an AutoCompleteArrayInput in an edit-form for the _titles_-resource. 

## Handling change of just referenced data

In case no changes are made to the resource _titles_, just to any of the referenced resources, say _subjects_ - as Save is pressed an update will be sent for both _titles_ and _subjects_ resources. The update statement for _titles_ contains no fields as there are no changes - that is correct. However should the update be sent at all or could we save a roundtrip? 

Previously one could use the hook _useReferenceManyToManyUpdate_ to intercept the save action and altering the diff. The empty update of _titles_ does no harm, but as I'm using a graphql endpoint the response contains no values and the apollo client expects at least an id in return else it barfs about `The response to 'update' must be like { data: { id: 123, ... } }, but the received data does not have an 'id' key. The dataProvider is probably wrong for 'update'`. So I'll get back `Server communication error` instead of `Saved`. 

I'm not sure where to handle this properly in v2 as the hook _useReferenceManyToManyUpdate_ has been removed. Is this supposed to be handled by the _ManyToManyReferenceContextProvider_ or in the dataProvider somehow?

This is what is sent to the graphql endpoint, notice the empty _set_-variable.

```JSON
{operationName: "update_metadata_titles", variables: {_set: {}, where: {id: {_eq: 8812}}},…}
```

## Changing just the resource, not any of the referenced data, hides the referenced data

In the editing form, changing just data of the titles-resource without touching the referenced data then the referenced data is hidden after pressing _Save_.

This is illustrated in the following screen recordings. In the recordings the _Base information_-tab contains just data from _titles_-resource and the _Attributes_-tab contains just many-to-many-referenced data. 

Just changing data of the resource makes the referenced data hide. In the end of the recording the screen is reloaded hard and the referenced data is shown again:

https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202021-01-12%20at%2009.59.13.mov

Referenced data that is changed will still be visible:

https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202021-01-12%20at%2010.30.45.mov

## ~~SelectArrayInput not populated on first visit to editform~~

~~A screen recording showing how one has to revisit the edit form of an item in order for the SelectArrayInput to be populated with the current values. If it was just that the optionText-prop was missing in the data then empty chipfields would be displayed.~~

~~https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202020-12-12%20at%2020.30.53.mov~~

## Prop perPage in ReferenceManyToManyInput

Haven't tested this yet as I'm now filtering the list of for example _conbtributors_ based on language.

~~The perPage prop should be honoured in the ReferenceManyToManyInput. Currently the response is limited to 25 relations. Hence, not all options are displayed in the SelectArrayInput options list, instead it renders empty chipfields.~~

~~https://github.com/v-bibliotek/temp/blob/master/ReferenceManyToManyInput%20-%2025%20limit%2C%20perPage%20not%20honoured.mov~~

~~A workaround that I'm using is to fetch the options using useQueryWithStore on mount and assing them to the choices-prop. However, this means another network request.~~

## ~~useReferenceManyToManyUpdate~~

### ~~Multiple many-to-many-related resources in one edit-form?~~
~~Can an edit form only save one many-to-many-relation using the hook `useReferenceManyToManyUpdate`? In other words, do I have to choose one of the fields _subject terms_, _authors_, _age categories_ or _genres_ to be editable in the same edit form?~~
