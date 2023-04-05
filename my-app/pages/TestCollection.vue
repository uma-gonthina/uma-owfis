<template>
  <collectionMain
    :url="url"
    :editUrl="editUrl"
    :entityId="1"
    :projectId="1"
    entity="CONTACTS"
  ></collectionMain>
</template>
<script setup lang="ts">
const url =
  "https://v1-orm-lib.mars.hipso.cc/notes/entity/CONTACTS/1?project_id=1&offset=0&limit=100&sort_column=id&sort_direction=desc";
const editUrl= "https://v1-orm-lib.mars.hipso.cc/notes";
</script>
