<template>
  <CRow>
    <CCol col="md-6">
      <CCard>
        <CCardHeader>
          <strong>Create new user</strong>
        </CCardHeader>
        <CCardBody>
          <CForm>
            <CInput
              placeholder="Full name"
              label="Name"
              :value.sync="user.name"
              horizontal
            />
            <CInput
              label="Email"
              placeholder="Enter your email"
              type="email"
              :value.sync="user.email"
              horizontal
              autocomplete="email"
            />
            <CInput
              label="Phone number"
              placeholder="Enter your tel"
              :value.sync="user.tel"
              horizontal
              autocomplete="tel"
            />
            <CInput label="Date of birth" type="date" :value.sync="user.birthday" horizontal />
            <CSelect
              label="Job title"
              horizontal
              :value.sync="user.title_id"
              :options="titles"
              placeholder="Please select"
            />
            <CSelect
              label="Subdivision"
              horizontal
              :value.sync="user.department_id"
              :options="departments"
              placeholder="Please select"
            />
            <CFormGroup class="form-group form-row">
              <template #label>
                <slot name="label">
                  <label class="col-form-label col-sm-3">Activated</label>
                </slot>
              </template>
              <template #input>
                <CSwitch class="mx-1" color="success" :checked.sync="user.active" />
              </template>
            </CFormGroup>
            <hr />
            <CInput
              placeholder="Enter the password."
              label="Password"
              type="password"
              :value.sync="user.password"
              horizontal
            />
            <CInput
              placeholder="Confirm password"
              label="Confirm"
              type="password"
              :value.sync="user.password_confirmation"
              horizontal
            />
          </CForm>
        </CCardBody>
        <CCardFooter>
          <CButton size="sm" @click="createUser" class="float-right" color="success">
            <CIcon name="cil-medical-cross" /> Create
          </CButton>
        </CCardFooter>
      </CCard>
    </CCol>
  </CRow>
</template>

<script>
import services from "../../services/factory";

export default {
  name: "Create",
  data() {
    return {
      user: {
        name: "",
        email: "",
        tel: "",
        birthday: "",
        title_id: "",
        department_id: "",
        active: true,
        password: "",
        password_confirmation: ""
      },
      titles: [],
      departments: []
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    async fetch() {
      const titleResponse = await services.title.all();
      this.titles = this.formatKeys(titleResponse.data);
      const departmentResponse = await services.department.all();
      this.departments = this.formatKeys(departmentResponse.data);
    },
    async createUser() {
      await services.user
        .create(this.user)
        .then(response => {
          this.$router.push({ path: `/users/${response.data.id}` });
          this.$toast.success("Account created");
        })
        .catch(error => {
          this.toastHttpError(error);
        });
    }
  }
};
</script>
