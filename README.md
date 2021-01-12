# Issues with ReferenceManyToManyInput

This may be issues related to my implementation rather than in the component itself.

Assume that the many-to-many-relation between resources is as follows:

```
titles <-> titles_subjects <-> subjects
titles <-> titles_contributors <-> contributors
titles <-> titles_attributes <-> attributes
```
So the editing form uses _titles_ as resource and _subjects_, _contributors_ and _attributes_ are referenced data.  

For example _subjects_ are to be selected from an SelectArrayInput or an AutoCompleteArrayInput in an edit-form for the _titles_-resource. 

## Changing just the resource, not any of the referenced data, hides the referenced data

In the editing form, if changing just data of the titles-resource without touching the referenced data then the referenced data is hidden after pressing _Save_.

This is illustrated in the following screen recording, where I change data of the title in the _Base information_-tab, then switch the _Attributes_-tab that contains the referenced data and then press save.

## ~~SelectArrayInput not populated on first visit to editform~~

~~A screen recording showing how one has to revisit the edit form of an item in order for the SelectArrayInput to be populated with the current values. If it was just that the optionText-prop was missing in the data then empty chipfields would be displayed.

~~https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202020-12-12%20at%2020.30.53.mov

## Prop perPage in ReferenceManyToManyInput

Haven't tested this yet as I'm now filtering the list of for example _conbtributors_ based on language.

~~The perPage prop should be honoured in the ReferenceManyToManyInput. Currently the response is limited to 25 relations. Hence, not all options are displayed in the SelectArrayInput options list, instead it renders empty chipfields.

~~https://github.com/v-bibliotek/temp/blob/master/ReferenceManyToManyInput%20-%2025%20limit%2C%20perPage%20not%20honoured.mov

~~A workaround that I'm using is to fetch the options using useQueryWithStore on mount and assing them to the choices-prop. However, this means another network request.

## ~~useReferenceManyToManyUpdate

### ~~Multiple many-to-many-related resources in one edit-form?
~~Can an edit form only save one many-to-many-relation using the hook `useReferenceManyToManyUpdate`? In other words, do I have to choose one of the fields _subject terms_, _authors_, _age categories_ or _genres_ to be editable in the same edit form?
