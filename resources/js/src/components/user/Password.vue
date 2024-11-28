<template>
  <CCard>
    <CCardHeader>
      <strong>Change password</strong>
    </CCardHeader>
    <CCardBody>
      <CForm>
        <CInput placeholder="Enter the password." label="Password" type="password" horizontal />
        <CInput
          placeholder="Enter a new password."
          label="New Password"
          type="password"
          :value.sync="password.password"
          horizontal
        />
        <CInput
          placeholder="Confirm password."
          label="Confirm"
          type="password"
          :value.sync="password.password_confirmation"
          horizontal
        />
      </CForm>
    </CCardBody>
    <CCardFooter>
      <CButton
        type="submit"
        @click="updatePassword"
        size="sm"
        class="float-right"
        color="success"
      >
        <CIcon name="cil-check" />Change
      </CButton>
    </CCardFooter>
  </CCard>
</template>

<script>
import services from "../../services/factory";

export default {
  name: "Password",
  props: {
    userId: {
      required: true,
    },
    isMe: {
      required: false,
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      password: {
        password_confirmation: "",
        password: "",
      },
    };
  },
  methods: {
    async updatePassword() {
      return await (this.isMe ? services.auth : services.user)
        .update(this.password, this.userId)
        .then((response) => {
          this.$toast.success("Modified");
        })
        .catch((error) => {
          this.toastHttpError(error);
        });
    },
  },
};
</script>
