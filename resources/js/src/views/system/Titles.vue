<template>
  <CRow>
    <CCol col="md-6">
      <CFormList :service="service" :fields="fields" :title="title" />
    </CCol>
  </CRow>
</template>

<script>
import services from "../../services/factory";
import CFormList from "../../components/form/List";

export default {
  name: "Titles",
  components: {
    CFormList
  },
  data() {
    return {
      fields: [
        { key: "id", label: "Code" },
        { key: "name", label: "Name" }
      ],
      service: services.title,
      title: "Job title"
    };
  }
};
</script>
