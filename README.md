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

## Inputs nested in ReferenceManyToManyInput in a create-form are populated from start

```
<Create
      {...props}
      title={<Heading />}
      actions={<TitleCreateActions />}
      onFailure={onFailure}
      onSuccess={onSuccess}
      undoable={false}
    >
      <ManyToManyReferenceContextProvider>
        <TabbedForm>
          <FormTab label="Attributes" {...props}>
            <ReferenceManyToManyInput
              source="id"
              reference="metadata_subjects"
              through="metadata_titles_subjects"
              using="title_id,subject_id"
              fullWidth
              label="Subjects"
              perPage={1000}
            >
              <SelectArrayInput
                source="type"
                optionValue="id"
                optionText="type"
                translateChoice={false}
              />
            </ReferenceManyToManyInput>

            <ReferenceManyToManyInput
              source="id"
              reference="metadata_contributors"
              through="metadata_titles_contributors"
              using="title_id,contributor_id"
              fullWidth
              label="Contributors"
              perPage={1000}
            >
              <AutocompleteArrayInput
                source="name"
                optionText="name"
                optionValue="id"
                // choices={contributorsChoices}
                translateChoice={false}
                suggestionLimit={30}
              />
            </ReferenceManyToManyInput>

            <ReferenceManyToManyInput
              source="id"
              reference="metadata_attributes"
              through="metadata_titles_attributes"
              using="title_id,attribute_id"
              fullWidth
              label="Attributes"
              perPage={1000}
            >
              <AutocompleteArrayInput
                source="name"
                optionText="name"
                optionValue="id"
                translateChoice={false}
                // shouldRenderSuggestions={(value) => value.length > 1}
                suggestionLimit={30}
              />
            </ReferenceManyToManyInput>
          </FormTab>
        </TabbedForm>
      </ManyToManyReferenceContextProvider>
    </Create>
```

Queries related to the populating the _Attributes_ _AutocompleteArrayInput_ in order:

```JSON
{
  "operationName": "metadata_titles_attributes",
  "variables": { "where": { "title_id": {} }, "limit": 25, "offset": 0, "order_by": { "id": "desc" } },
  "query": "query metadata_titles_attributes($limit: Int, $offset: Int, $order_by: [metadata_titles_attributes_order_by!]!, $where: metadata_titles_attributes_bool_exp) {\n  items: metadata_titles_attributes(limit: $limit, offset: $offset, order_by: $order_by, where: $where) {\n    attribute_id\n    id\n    title_id\n    __typename\n  }\n  total: metadata_titles_attributes_aggregate(order_by: $order_by, where: $where) {\n    aggregate {\n      count\n      __typename\n    }\n    __typename\n  }\n}\n"
}

{
  "operationName": "metadata_attributes",
  "variables": { "where": { "_and": [] }, "limit": 25, "offset": 0, "order_by": { "id": "desc" } },
  "query": "query metadata_attributes($limit: Int, $offset: Int, $order_by: [metadata_attributes_order_by!]!, $where: metadata_attributes_bool_exp) {\n  items: metadata_attributes(limit: $limit, offset: $offset, order_by: $order_by, where: $where) {\n    description\n    id\n    name\n    type\n    __typename\n  }\n  total: metadata_attributes_aggregate(order_by: $order_by, where: $where) {\n    aggregate {\n      count\n      __typename\n    }\n    __typename\n  }\n}\n"
}


{
  "operationName": "metadata_attributes",
  "variables": { "where": { "id": { "_in": [3, 1, 4, 2] } } },
  "query": "query metadata_attributes($where: metadata_attributes_bool_exp) {\n  items: metadata_attributes(where: $where) {\n    description\n    id\n    name\n    type\n    __typename\n  }\n  total: metadata_attributes_aggregate(where: $where) {\n    aggregate {\n      count\n      __typename\n    }\n    __typename\n  }\n}\n"
}
```


## Prop perPage in ReferenceManyToManyInput

Tested in a create form - the _perPage_ of _ReferenceManyToManyInput_ is not honoured. After setting _perPage={1000}_ the query still limits the query of the associative table to 25.

```HTML
            <ReferenceManyToManyInput
              source="id"
              reference="metadata_subjects"
              through="metadata_titles_subjects"
              using="title_id,subject_id"
              fullWidth
              label="Subjects"
              perPage={1000}
            >
              <SelectArrayInput
                source="type"
                optionValue="id"
                optionText="type"
                translateChoice={false}
              />
            </ReferenceManyToManyInput>
````

And the queries executed to populate the subjects _SelectArrayInput_:


```JSON
{
  "operationName": "metadata_titles_subjects",
  "variables": { "where": { "title_id": {} }, "limit": 25, "offset": 0, "order_by": { "id": "desc" } },
  "query": "query metadata_titles_subjects($limit: Int, $offset: Int, $order_by: [metadata_titles_subjects_order_by!]!, $where: metadata_titles_subjects_bool_exp) {\n  items: metadata_titles_subjects(limit: $limit, offset: $offset, order_by: $order_by, where: $where) {\n    id\n    subject_id\n    title_id\n    __typename\n  }\n  total: metadata_titles_subjects_aggregate(order_by: $order_by, where: $where) {\n    aggregate {\n      count\n      __typename\n    }\n    __typename\n  }\n}\n"
}
```

```JSON
{
  "operationName": "metadata_subjects",
  "variables": { "where": { "_and": [] }, "limit": 25, "offset": 0, "order_by": { "id": "desc" } },
  "query": "query metadata_subjects($limit: Int, $offset: Int, $order_by: [metadata_subjects_order_by!]!, $where: metadata_subjects_bool_exp) {\n  items: metadata_subjects(limit: $limit, offset: $offset, order_by: $order_by, where: $where) {\n    description\n    id\n    targetgroup_id\n    type\n    __typename\n  }\n  total: metadata_subjects_aggregate(order_by: $order_by, where: $where) {\n    aggregate {\n      count\n      __typename\n    }\n    __typename\n  }\n}\n"
}
```

```JSON
{
  "operationName": "metadata_subjects",
  "variables": { "where": { "id": { "_in": [38, 41, 40, 39, 43, 42, 47, 13, 7, 1, 10, 3, 8, 11, 2] } } },
  "query": "query metadata_subjects($where: metadata_subjects_bool_exp) {\n  items: metadata_subjects(where: $where) {\n    description\n    id\n    targetgroup_id\n    type\n    __typename\n  }\n  total: metadata_subjects_aggregate(where: $where) {\n    aggregate {\n      count\n      __typename\n    }\n    __typename\n  }\n}\n"
}

```

~~The perPage prop should be honoured in the ReferenceManyToManyInput. Currently the response is limited to 25 relations. Hence, not all options are displayed in the SelectArrayInput options list, instead it renders empty chipfields.~~

~~https://github.com/v-bibliotek/temp/blob/master/ReferenceManyToManyInput%20-%2025%20limit%2C%20perPage%20not%20honoured.mov~~

~~A workaround that I'm using is to fetch the options using useQueryWithStore on mount and assing them to the choices-prop. However, this means another network request.~~

## Improvement: filter reference table

A nice feature would be to be able to filter data returned from the reference table. In this pseudocode only the _subjects_ having _targetgroup_id = 1_ will be available as options in the _SelectArrayInput_.

```HTML
      <ReferenceManyToManyInput
        source="id"
        reference="subjects"
        filterReference={{targetgroup_id: 1}}
        through="titles_subjects"
        using="title_id,subject_id"
        label="Subjects"
      >
        <SelectArrayInput
          source="name"
          optionValue="id"
          optionText="name"
          translateChoice={false}
        />
      </ReferenceManyToManyInput>
```

## Improvement: Handling change of just referenced data

In case no changes are made to the resource _titles_, just to any of the referenced resources, say _subjects_ - as Save is pressed an update will be sent for both _titles_ and _subjects_ resources. The update statement for _titles_ contains no fields as there are no changes - that is correct. However should the update be sent at all or could we save a roundtrip? 

The empty update of _titles_ does no harm, but as I'm using a graphql endpoint the response contains no values and the apollo client expects at least an id in return else it barfs about `The response to 'update' must be like { data: { id: 123, ... } }, but the received data does not have an 'id' key. The dataProvider is probably wrong for 'update'`. So I'll get back `Server communication error` instead of `Saved`. 

Previously one could use the hook _useReferenceManyToManyUpdate_ to intercept the save action. I'm not sure where to handle this properly in v2 as the hook _useReferenceManyToManyUpdate_ has been removed. It sounds like a responsibility to the dataprovider, am I right?

This is what is sent to the graphql endpoint, notice the empty _set_-variable.

```JSON
{operationName: "update_metadata_titles", variables: {_set: {}, where: {id: {_eq: 8812}}},…}
```

## Changing just the resource, not any of the referenced data, hides the referenced data after save

Bug? 

In the editing form, changing just data of the titles-resource without touching the referenced data then the referenced data is hidden after pressing _Save_.

This is illustrated in the following screen recordings. In the recordings the _Base information_-tab contains just data from _titles_-resource and the _Attributes_-tab contains just many-to-many-referenced data. 

Just changing data of the resource makes the referenced data hide. In the end of the recording the screen is reloaded hard and the referenced data is shown again:

https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202021-01-12%20at%2009.59.13.mov

Referenced data that is changed will still be visible:

https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202021-01-12%20at%2010.30.45.mov


## ~~SelectArrayInput not populated on first visit to editform~~

~~A screen recording showing how one has to revisit the edit form of an item in order for the SelectArrayInput to be populated with the current values. If it was just that the optionText-prop was missing in the data then empty chipfields would be displayed.~~

~~https://github.com/v-bibliotek/temp/blob/master/Screen%20Recording%202020-12-12%20at%2020.30.53.mov~~

## ~~useReferenceManyToManyUpdate~~

### ~~Multiple many-to-many-related resources in one edit-form?~~
~~Can an edit form only save one many-to-many-relation using the hook `useReferenceManyToManyUpdate`? In other words, do I have to choose one of the fields _subject terms_, _authors_, _age categories_ or _genres_ to be editable in the same edit form?~~
