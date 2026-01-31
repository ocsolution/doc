# OCTable

Display data in a table.

## Props

### + data
Use the `data` prop to set the data to display in the table. By default, the table will display all the fields of the rows.

```vue
<script setup>
const people = [{
  id: 1,
  name: 'Lindsay Walton',
  title: 'Front-end Developer',
  email: 'lindsay.walton@example.com',
  role: 'Member'
}, {
  id: 2,
  name: 'Courtney Henry',
  title: 'Designer',
  email: 'courtney.henry@example.com',
  role: 'Admin'
}, {
  id: 3,
  name: 'Tom Cook',  
  title: 'Director of Product',
  email: 'tom.cook@example.com',
  role: 'Member'
}]
</script>

<template>
  <OCTable :data="people" />
</template>
```

### + api
Use the `api` prop to get from server side to display in the table. but prop `columns` is required.
  - if **api** method of api is `GET` then **api** prop gets a string value.
  - if **api** method of api is `POST` then **api** prop gets a object value.

```vue
<template>
   <OCTable :api="api" :columns="columns"/>
</template>

<script setup>
// when api use with method GET
const api = 'api/table/list'

// when api use with method POST
const api = { url:'api/table/list', method:'POST' }

const columns = [
  { title: 'Code', data: 'Code', class: 'min-width' },
  { title: 'Name', data: 'Name', class: 'min-width' },
  { title: 'EnglishName', data: 'EnglishName', class: 'min-width' },
  { title: 'Action', data: null, class: 'min-width', render:'#action' },
]
</script>
```

### + columns
Use the `columns` prop to configure which columns to display. It's an array of objects with the following properties:
- **title**: Column header name shown in the table.
- **data**: Specifies which property of the row data this column displays. Can be dot notation ('user.name') or a function.
- **className**: Set custom class for cells in this column.
- **render**: Customize how cell content is rendered (raw HTML, formatting, etc).
- **orderable**: Allow/disallow sorting for this column. Default: true.
- **sortData**: Customize sorting more data for this column.
- **hidden**:  Allow/disallow hide this column. Default: false.
- **alignRight**: Allow/disallow text align right for this column. Default: false.

```vue
<template>
   <OCTable :data="data" :columns="columns"/>
</template>

<script setup>
const data = [
  { Id:1, Code:'Emp00001', Name:'ឡុង​ ឡងឌី', EnglishName:'Long LunDy', Age:24 },
  { Id:2, Code:'Emp00002', Name:'ផាន ឈៀងហេង', EnglishName:'Phang ChheangHeng', Age:24 },
]

const columns = [
  {
    title: "Employee",
    data: null,
    orderable: false,
    className: "all min-width",
    render: '#employee',
    sortData:[
      { title: 'Employee Code', data: 'Code' },
      { title: 'Employee Name', data: 'Name' },
      { title: 'Employee EnglishName', data: 'EnglishName' },
    ]
  },
  { title: '', data: 'Code', class: 'min-width', hidden: true,},
  { title: '', data: 'Name', class: 'min-width', hidden: true,},
  { title: '', data: 'EnglishName', class: 'min-width', hidden: true,},
  {
    title: "Age",
    data: "Age",
    alignRight: true,
    className: "min-width",
  },
  {
    title: "Actions",
    data: null,
    orderable: false,
    className: "all min-width",
    render:'#actions'
  }
]
</script>
```

### + options 
Usw the `options` allows customization of the table’s interface and available features. These `options` are set during initialization and control layout, behavior, and interactivity.
- **isScroll**: (Custom) A flag to conditionally enable scroll-related styles or logic in the component. Default: false.
- **scrollY**: Enables vertical scrolling and sets a fixed height (e.g., 400, '400px'). Automatically disables pagination if the height is large enough to show all rows. Default: 400.
- **scrollX**: Enables horizontal scrolling. Useful when the table width exceeds its container. Default: false
- **info**: Displays footer text such as "Total Records: 0". Default: true.
- **searching**: 	Enables the built-in search input for client-side filtering. Default: true.
- **paging**: Enables pagination and select record. Default: true.
- **reload**: Enables button reload. Default: true.
- **lengthMenu**: A dropdown menu allowing the user to select how many rows to show per page. Default: [10, 25, 50, 100].
- **pageLength**: Sets the default number of rows displayed per page. Default: 10.
  
You find more options in [datatable.net](https://datatables.net/reference/option/)

```vue
<script setup>
const optionDefault = {
    isScroll: true,
    scrollY: 400,
    scrollX: false,
    info: true,
    searching: true,
    paging: true,
    reload: true,
    responsive: {
      details: {
        renderer: DataTablesCore.Responsive.renderer.listHiddenNodes(),
      },
    },
    lengthMenu: [10, 25, 50, 100],
    pageLength: 10,

  };
</script>
```

### + selectedPk and v-model
The `selectedPk` defines the primary key field used to uniquely identify each row in the table (as a string). Use `v-model` to enable row selection in the table. The `v-model` will be an array containing the selected rows based on the `selectedPk` value.

```vue
<template>
   <OCTable v-model="selected" selectedPk="Code" :api="api" :columns="columns"/>
</template>

<script setup>
const api = { url:'api/table/list', method:'POST' }
const selected = ref()
const columns = [
  { title: 'Code', data: 'Code', class: 'min-width' },
  { title: 'Name', data: 'Name', class: 'min-width' },
  { title: 'EnglishName', data: 'EnglishName', class: 'min-width' },
  { title: 'Action', data: null, class: 'min-width', render:'#action' },
]
</script>
```

### + stateKey
Use the `stateKey` to enable `OCTable` to remember its state (including filters, sorting, and pagination) when navigating to a detail page and returning to the index page.
The `stateKey` must be a unique string used as the identifier for saving and restoring the table state.

```vue
<template>
   <OCTable stateKey="stateOCTable" :api="api" :columns="columns"/>
</template>

<script setup>
const api = { url:'api/table/list', method:'POST' }
const columns = [
  { title: 'Code', data: 'Code', class: 'min-width' },
  { title: 'Name', data: 'Name', class: 'min-width' },
  { title: 'EnglishName', data: 'EnglishName', class: 'min-width' },
  { title: 'Action', data: null, class: 'min-width', render:'#action' },
]
</script>
```

### + keyTable
Use the `keyTable` to identify a DOM element uniquely so Vue can efficiently re-render or re-create it when `keyTable` changes value.

```vue
<template>
  <OCWrapperPage>
    <OCBtn type="reload" color="reload" icon="reload" lable="Change Key" @click="keyTable = !keyTable"/>
    <OCTable :keyTable="" :api="api" :columns="columns"/>
  </OCWrapperPage>
</template>

<script setup>
const api = { url:'api/table/list', method:'POST' }
const keyTable = ref(false)
const columns = [
  { title: 'Code', data: 'Code', class: 'min-width' },
  { title: 'Name', data: 'Name', class: 'min-width' },
  { title: 'EnglishName', data: 'EnglishName', class: 'min-width' },
  { title: 'Action', data: null, class: 'min-width', render:'#action' },
]
</script>
```

### + showTemplate
Use the `showTemplate` to hide the default table rows and columns, and instead display a custom grid or card layout using a template. When `showTemplate` is set to `true`, you must also provide a `<template v-slot:template="[data]">` slot for rendering the custom content. Default: false.

```vue
<template>
   <OCTable :showTemplate="true" :api="api" :columns="columns">
      <template #template="[data]">
        <div class="w-full grid grid-cols-[repeat(auto-fill,minmax(263px,1fr))] items-center gap-3">
          <div v-for="d in data">
            <div>{{d.Code}} • {{d.EnglishName}}</div>
          </div>
        </div>
      </template>
   </OCTable>
</template>

<script setup>
const api = { url:'api/table/list', method:'POST' }
const columns = [
  { title: 'Code', data: 'Code', class: 'min-width' },
  { title: 'Name', data: 'Name', class: 'min-width' },
  { title: 'EnglishName', data: 'EnglishName', class: 'min-width' },
  { title: 'Action', data: null, class: 'min-width', render:'#action' },
]
</script>
```

### + isTotalDetail
Use the `isTotalDetail` to hide the OCTableFooter. When `isTotalDetail` is set to `true`, the footer will be hidden. Default: false

### + haveBorder
Use the `haveBorder` to apply a border style to the `OCTable`. When `haveBorder` is set to `true`, the table will be displayed with borders. Default: false.

### + autoload
Use the `autoload` in combination with the `api` prop to control whether data is automatically loaded from the server. When `autoload` is set to `false`, data will not be fetched automatically. To manually trigger data loading, use the exposed `ref` and call the `reload` function. Default: true.

### + selectAllPk
Use the `selectAllPk` to display a "Select All" button that selects all rows in the table.The value of selectAllPk should be a string that matches the unique key property in `data` prop or data from `api` prop.

### + hideGroupBtnSelect
Use the `hideGroupBtnSelect` to hide the "Select All" button group. When set to `true`, the selection controls will not be displayed. Default: true.

## Slots
You can use `slots` to customize the header, the footer and data cells of the table.

### + headerTable
Use the `#headerTable` slot to allows the parent to inject custom content above the table header, often used for extra actions or title.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #headerTable>
        <h3>My Table Title</h3>
      </template>
   </OCTable>
</template>
```

### + headerLeft
Use the `#headerLeft` slot to custom content on the left side of the table header, such as filter button.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #headerLeft>
        <OCTblFilter v-model="filter" state-key="stateTable" :inputs="inputs" width="w-[420px]" @onClick="fnClickFilter"/>
      </template>
   </OCTable>
</template>
```

### + headerCenter
Use the `#headerCenter` slot to custom content in the center of the header.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #headerCenter>
        <div>Center Header Content</div>
      </template>
   </OCTable>
</template>
```

### + headerApplyFilter
Use the `#headerApplyFilter` slot to injecting custom filter apply buttons or chip components in the header.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #headerApplyFilter>
        <OCTblChip v-model="chips" state-key="stateTable" @on-remove="fnRemoveChip" />
      </template>
   </OCTable>
</template>
```

### + headerRight
Use the `#headerRight` slot to custom content on the right side of the table header, e.g. export buttons or additional actions.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #headerRight>
        <OCBtns :data="btnHeader" @click="eventBtnHeader" />
      </template>
   </OCTable>
</template>
```

### + middleTable
Use the `#middleTable` slot to render custom content between header and the table

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #middleTable>
        <div>Middle Table</div>
      </template>
   </OCTable>
</template>
```

### + loading
Use the `#loading` slot to custom content shown during loading.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #loading>
        <MyCustomSpinner />
      </template>
   </OCTable>
</template>
```

### + template
Use the `#template` slot to custom rendering for a card layout or alternate view instead of a table (like a grid or list).**v-bind="[dataTemplate]"** means you're passing the whole dataTemplate array as slot props.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #template="[items]">
        <CardList :data="items" />
      </template>
   </OCTable>
</template>
```

### + totalDetail
Use the `#totalDetail` slot to custom showing total summary info under the table.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #totalDetail="{items}">
        <CardList :data="items" />
      </template>
   </OCTable>
</template>
```

### + ShowDetailAfterTableOne
Use the `#ShowDetailAfterTableOne` slot to custom content below the table (first position), like additional detail cards or logs.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #ShowDetailAfterTableOne>
         <MoreDetails />
      </template>
   </OCTable>
</template>
```

### + ShowDetailAfterTableTwo
Use the `#ShowDetailAfterTableTwo` slot to show more content after the table—useful if you want to split content into two zones.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #ShowDetailAfterTableOne>
         <MoreDetails />
      </template>
   </OCTable>
</template>
```

### + Dynamic slots 
Dynamic column slot based on the column name and use the `Dynamic slots` slot to allows the parent component to define a custom cell renderer for each column.

```vue
<template>
   <OCTable :api="api" :columns="columns">
      <template #username="props">
        <strong>{{ props.cellData }}</strong>
      </template>

      <template #actions="props">
        <button @click="edit(props.rowData)">Edit</button>
      </template>
   </OCTable>
</template>
```

## Emits
Use to declares the custom events that the component can emit to the parent.

### + @update:data=""
Use to inform the parent component that the request is about to be sent and what the filter/sort/page/search request payload (dataSender).

```vue
<template>
   <OCTable :api="api" :columns="columns" @update:data="fnUpdateData"/>
</template>

<script setup>
function fnUpdateData(d){
    d.Id = 1
    d.PeriodCode = 10213
    d.Type = 'Department'
}
</script>
```

### + @update:dataSrc=""
Once the data is successfully fetched from the API, this event sends the full data or custom data to the table component.

```vue
<template>
   <OCTable :api="api" :columns="columns" @update:dataSrc="fnUpdateDataSrc"/>
</template>

<script setup>
const storeDataTable = ref()
function fnUpdateDataSrc(d){
    d.dataSrc = d.data
    storeDataTable.value = d.dataSrc
}
</script>
```

### + @onSaveState=""
This emits the entire table state, useful when you're using state persistence with stateKey. The parent or a pinia store will save the table’s filter/order/page/data config.

### + @actionCheck=""
The event `actionCheck` is emitted whenever a checkbox row is selected or deselected in the DataTable. This lets the parent component know what has been selected or unselected.

```vue
<template>
   <OCTable :api="api" :columns="columns" @actionCheck="handleChecked"/>
</template>

<script setup>
function handleChecked(data) {
  if (Array.isArray(data)) {
    console.log("Remaining selected rows:", data);
  } else {
    console.log("New row selected:", data);
  }
}
</script>
```

### + @reloadByFixedData=""
is emitting a custom event called "reloadByFixedData" from inside your table component to inform the parent component to perform a reload when:
- The table is using fixed (static) data (i.e., no external API)
- You need the parent to refresh or change the data source (maybe trigger a data fetch or state update).

```vue
<template>
   <OCTable :data="myStaticData" :columns="columns" @reloadByFixedData="fetchNewStaticData"/>
</template>

<script setup>
const myStaticData = ref()
function fetchNewStaticData() {
  // maybe pull from localStorage or re-filter something
  myStaticData.value = [...getFilteredList()]
}
</script>
```

### + @onOrder=""
The event `actionCheck` is emitted an event from the table component to the parent component with the current sorting order (i.e., which column and what direction).

```vue
<template>
   <OCTable :data="data" :columns="columns" @onOrder="handleOrder"/>
</template>

<script setup>
function handleOrder(order) {
  const [colIndex, dir] = order[0];
  console.log(`Sorted column ${colIndex} in ${dir} order`);
  // maybe: save to state or trigger something
}

</script>
```

### + @onChangePagination=""
Use to let the parent know the pagination state.

```vue
<template>
   <OCTable :data="data" :columns="columns" @onChangePagination="handlePagination"/>
</template>

<script setup>
function handlePagination(currentPage, pageSize) {
  console.log("User moved to page", currentPage);
  console.log("Records per page:", pageSize);

  // Optional: Save to state
  // savePaginationState({ page: currentPage, pageSize });
}
</script>
```

### + @onSelectRecord=""
Use to inform the parent of page size change so it can adjust state or fetch accordingly.

```vue
<template>
   <OCTable :data="data" :columns="columns" @onSelectRecord="handleRecordSelection"/>
</template>

<script setup>
function handleRecordSelection(recordsPerPage, currentPage) {
  console.log("Records per page:", recordsPerPage); // e.g., 25
  console.log("Current page:", currentPage);        // e.g., 1

  // Optional: store preferences or trigger other actions
  // saveTableSettings({ recordsPerPage, page: currentPage })
}
</script>
```

### + @onSearching=""
This emit notifies the parent component that a search operation has occurred — and passes the search keyword to the parent.

```vue
<template>
   <OCTable :data="data" :columns="columns" @onSearching="handleSearching"/>
</template>

<script setup>
function handleSearching(search) {
  console.log("search:", search); // e.g., Lundy
}
</script>
```

## Expose
Used to expose internal functions, refs, or variables from a component so the parent can access and use them via `ref`.
It has function such as { **dt()**, **filter()**, **reload()**, **responsive()** }.
- **dt()**: Returns the DataTable instance (for direct manipulation).
- **filter()**: Fetches data from the API and updates the table (with page reset).
- **reload()**: Reloads data (optional: keep the current page or not).
- **responsive()**: Forces the DataTable to recalculate its responsive layout.

```vue
<template>
   <OCTable ref="ocTableRef" :api="api" :columns="columns"/>
</template>

<script setup>
const ocTableRef = ref();

// Access exposed method
function refreshTable() {
  ocTableRef.value?.reload(); // Calls exposed reload() in child
}

</script>
```




