<template>
  <section>
    <section class="step" id="step-3">
      <!-- block step2 to create -->
      <div class="step-3__block">
        <div class="step__group">
          <div class="step-control">
            <div class="step-control__header">
              <label for="">Lý do muốn ứng tuyển vào công ty</label>
            </div>
            <textarea
              name="reason"
              id=""
              cols="30"
              rows="10"
              v-model="ConfirmForm.reason"
              @input="checkRes"
              @blur="checkRes"
            ></textarea>
            <div class="count_reason">{{ ConfirmForm.reason.length }}/1000</div>
            <p class="error" v-if="error.reason.status">
              {{ error.reason.message }}
            </p>
          </div>
          <div class="step-control">
            <div class="step-control__header">
              <label for="">Mức lương mong muốn</label>
            </div>
            <input
              type="number"
              name="salary"
              id="salary"
              @input="checkSalary"
              @blur="checkSalary"
              v-model="ConfirmForm.salary"
              :class="{ errorBorder: error.salary.status }"
            />
            <span>VND</span>
          </div>
          <p class="error" v-if="error.salary.status">
            {{ error.salary.message }}
          </p>
        </div>
      </div>
      <!-- end block step 2  -->
    </section>
    <button class="nav_btn prevBtn" @click="preStep">Quay lại</button>
    <button class="nav_btn backBtn" @click="save">Hoàn thành</button>
  </section>
</template>

<script>
export default {
  name: "ConfirmCom",
  emits: ["preStep", "finishForm"],
  data: function () {
    return {
      ConfirmForm: {
        reason: "",
        salary: 0,
      },
      error: {
        salary: {
          message: "",
          status: false,
        },
        reason: {
          message: "",
          status: false,
        },
      },
      messages: {
        REQUIRED: "không được để trống",
        MAXLENGTH: "Chỉ được nhập tối da 10 ký tứ",
        NAN: "không phải là số ",
      },
    };
  },
  methods: {
    save() {
      console.log(this.ConfirmForm);
      this.$emit("finishForm", this.ConfirmForm);
    },
    preStep() {
      this.$emit("preStep");
    },
    checkRes() {
      const res = this.ConfirmForm.reason.length;
      if (!res) {
        this.error.reason = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (res >= 1000) {
        this.error.reason = {
          status: true,
          message: this.messages.MAXLENGTH,
        };
      } else {
        this.error.reason = false;
      }
    },
    checkSalary() {
      const regex = /^\d+$/;
      const salary = this.ConfirmForm.salary;
      if (!salary.length) {
        this.error.salary = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (!salary.match(regex)) {
        this.error.salary = {
          status: true,
          message: this.messages.NAN,
        };
      } else if (salary.length > 10) {
        this.error.salary = {
          status: true,
          message: this.messages.MAXLENGTH,
        };
      } else this.error.salary.status = false;
    },
  },
};
</script>

<style>
span {
  margin-left: -40px;
}
.input[id="salary"] {
  width: 128px;
}
</style>
