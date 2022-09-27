<template>
  <section class="step" id="step-1">
    <form v-on:submit.prevent>
      <div class="step__group">
        <div class="step-control">
          <div class="step-control__header">
            <button>Must</button>
            <label for="">Họ và tên</label>
          </div>
          <input
            type="text"
            id="userName"
            name="userName"
            v-model="userInfoForm.userName"
            @input="checkUserName"
            @blur="checkUserName"
            :class="{ errorBorder: error.userName.status }"
          />
          <p class="error" v-if="error.userName.status">
            {{ error.userName.message }}
          </p>
        </div>

        <div class="step-control dob">
          <div class="step-control__header">
            <button>Must</button>
            <label for="">Ngày sinh</label>
          </div>
          <date-picker
            valueType="format"
            name="userDOB"
            v-model="userInfoForm.userDOB"
            @input="checkUserDOB"
            @blur="checkUserDOB"
            :class="{ errorBorder: error.userDOB.status }"
          ></date-picker>
          <p class="error" v-if="error.userDOB.status">
            {{ error.userDOB.message }}
          </p>
        </div>
        <div class="step-control">
          <div class="step-control__header">
            <button>Must</button>
            <label for="">Thành phố</label>
          </div>
          <SelectCom
            :list="listProvinces"
            :selectValue.sync="userInfoForm.userCity"
          ></SelectCom>
        </div>
        <div class="step-control position">
          <div class="step-control position--header">
            <label for="">Vị trí làm việc</label>
            <small>Có thể chọn nhiều vị trí mà bạn muốn làm việc.</small>
          </div>
          <MutiSelect
            :listData="listUserPosition"
            v-model="userInfoForm.userPosition"
            @updatePosition="updatePosition"
          />
        </div>
        <div class="step-control">
          <div class="step-control__header">
            <label for="">Mô tả về bản thân</label>
          </div>
          <textarea
            cols="10"
            rows="5"
            name="userDesc"
            v-model="userInfoForm.userDesc"
            :class="{ errorBorder: error.userDesc.status }"
            @input="checkUserDesc"
            @blur="checkUserDesc"
            maxlength="1000"
          ></textarea>
          <div class="count_desc">{{ userInfoForm.userDesc.length }}/1000</div>
          <p class="error" v-if="error.userDesc.status">
            {{ error.userDesc.message }}
          </p>
        </div>
        <div class="step-control upload">
          <div class="step-control__header">
            <label for="">Ảnh cá nhân</label>
          </div>
          <UploadFile
            v-model="userInfoForm.userAvatar"
            @handleUpload="handleUpload"
            name="userAvatar"
          />
        </div>
      </div>
    </form>
    <!-- nav_btn -->
    <button class="nav_btn" @click="nextStep">Next</button>
  </section>
</template>

<script>
import DatePicker from "vue2-datepicker";
import UploadFile from "./uploadfile/index.vue";
import SelectCom from "../Select.vue";
import MutiSelect from "./mutiSelect/index.vue";
import { UserPosition } from "@/data/index.js";
import axios from "axios";

export default {
  name: "UserInfo",
  data: function () {
    return {
      listProvinces: [],
      userInfoForm: {
        userName: "",
        userCity: "",
        userPosition: "",
        userDOB: null,
        userDesc: "",
        userAvatar: null,
      },
      error: {
        userName: {
          message: "",
          status: false,
        },
        userDOB: {
          message: "",
          status: false,
        },
        userDesc: {
          message: "",
          status: false,
        },
      },
      messages: {
        REQUIRED: "Không được để trống",
        MAXLENGTH: "Chỉ cho phép độ dài kí tự từ 1-100",
        VALIDDATE: "Chỉ được chọn ngày trong quá khứ ",
      },
      listUserPosition: UserPosition,
    };
  },
  emits: ["nextStep", "finishForm"],
  methods: {
    getList() {
      axios
        .get("https://provinces.open-api.vn/api/?depth=1")
        .then((response) => {
          response.data.map((item) => {
            this.listProvinces.push(item);
          });
        });
    },
    getDropDownData(itemValue) {
      this.userInfoForm.userCity = itemValue;
    },
    updatePosition(itemValue) {
      this.userInfoForm.userPosition = itemValue;
    },
    nextStep() {
      this.checkUserName();
      this.checkUserDOB();
      this.checkUserDesc();
      if (
        !this.error.userName.status &&
        !this.error.userDOB.status &&
        !this.error.userDesc.status
      ) {
        this.$emit("finishForm", this.userInfoForm);
        this.$emit("nextStep");
      }
    },
    checkUserName() {
      let nameLength = this.userInfoForm.userName.length;
      if (!nameLength) {
        this.error.userName = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (nameLength > 100) {
        this.error.userName = {
          message: this.messages.MAXLENGTH,
        };
      } else this.error.userName.status = false;
    },

    checkUserDOB() {
      const dateNow = Date.parse(new Date());
      const dateSelect = Date.parse(this.userInfoForm.userDOB);
      if (!dateSelect || dateSelect === "NaN") {
        this.error.userDOB = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (dateNow <= dateSelect) {
        this.error.userDOB = {
          status: true,
          message: this.messages.VALIDDATE,
        };
      } else {
        this.error.userDOB = false;
      }
    },
    checkUserDesc() {
      let desc = this.userInfoForm.userDesc.length;
      if (!desc) {
        this.error.userDesc = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (desc >= 1000) {
        this.error.userDesc = {
          status: true,
          message: this.messages.MAXLENGTH,
        };
      } else this.error.userDesc.status = false;
    },
    handleUpload(e) {
      let file = e.target.files[0] || e.dataTransfer.files;
      this.userInfoForm.userAvatar = file;
      console.log(file);
    },
  },
  created() {
    this.getList();
  },
  components: {
    DatePicker,
    UploadFile,
    SelectCom,
    MutiSelect,
  },
};
</script>
