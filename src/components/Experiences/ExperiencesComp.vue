<template>
  <section>
    <section class="step" id="step-2">
      <form action="" v-on:submit.prevent>
        <div
          class="step-2__block"
          v-for="item in formExperiences"
          :key="item.id"
          :class="{ errorBorder: error.company.status }"
        >
          <div class="step__group">
            <div class="step-control company">
              <SelectCom
                :list="company"
                :selectValue.sync="item.company"
              ></SelectCom>
              <button @click="removeCompany(item.id)">
                <img src="@/assets/icons/trash.svg" alt="" />
              </button>
            </div>
            <div class="step-control">
              <div class="step-control__header">
                <button>Must</button>
                <label for="">Vị trí từng làm</label>
              </div>
              <input
                type="text"
                id="exPosition"
                name="exPosition"
                v-model="item.exPosition"
                @input="checkExPosition(item)"
                @blur="checkExPosition(item)"
                :class="{ errorBorder: error.exPosition.status }"
              />
              <p class="error" v-if="error.exPosition.status">
                {{ error.exPosition.message }}
              </p>
            </div>
            <div class="step-control">
              <div class="step-control__header">
                <button>Must</button>
                <label for="">Thời gian làm việc</label>
              </div>
              <div class="step-control__conten">
                <date-picker
                  valueType="format"
                  class="timeStared"
                  v-model="item.timeStared"
                  @input="checkDateExp(item)"
                  @blur="checkDateExp(item)"
                  :class="{ errorBorder: error.timeStared.status }"
                >
                </date-picker>
                <span class="inti"> - </span>
                <date-picker
                  valueType="format"
                  class="timeEnded"
                  v-model="item.timeEnded"
                  @input="checkDateExp(item)"
                  @blur="checkDateExp(item)"
                  :class="{ errorBorder: error.timeEnded.status }"
                ></date-picker>
              </div>
              <div class="error_bounder">
                <p class="error" v-if="error.timeStared.status">
                  {{ error.timeStared.message }}
                </p>
                <p class="error" v-if="error.timeEnded.status">
                  {{ error.timeEnded.message }}
                </p>
                <p class="error" v-if="error.checkTime.status">
                  {{ error.checkTime.message }}
                </p>
              </div>
            </div>

            <div class="step-control">
              <div class="step-control__header">
                <label for="">Mô tả công việc</label>
              </div>
              <textarea
                name="exDesc"
                cols="10"
                rows="5"
                v-model="item.exDesc"
                :class="{ errorBorder: error.exDesc.status }"
                @input="exDesc(item)"
                @blur="exDesc(item)"
              ></textarea>
              <div class="count_desc">{{ item.exDesc.length }}/1000</div>
              <p class="error" v-if="error.exDesc.status">
                {{ error.exDesc.message }}
              </p>
            </div>
          </div>
        </div>
        <p class="error" v-if="error.company.status">
            {{ error.company.message }}
          </p>
      </form>
    </section>
    <div class="block_navigate">
      <button class="nav_btn btn_createBlock" @click="addBlock">
        + Thêm công ty
      </button>
    </div>
    <button class="nav_btn prevBtn" @click="nextStep">Tiếp</button>
    <button class="nav_btn backBtn" @click="preStep">Quay lại</button>
  </section>
</template>

<script>
import { UserPosition, ListCompany } from "@/data";
import DatePicker from "vue2-datepicker";
import shortid from "shortid";
import SelectCom from "../Select.vue";
export default {
  name: "ExperiencesComp",
  emits: ["preStep", "nextStep", "finishForm"],
  data: function () {
    return {
      formExperiences: [
        {
          id: shortid.generate(),
          exPosition: "",
          timeStared: "",
          timeEnded: "",
          exDesc: "",
        },
      ],
      error: {
        company: {
          message: "",
          status: false,
        },
        exDesc: {
          message: "",
          status: false,
        },
        exPosition: {
          message: "",
          status: false,
        },
        timeStared: {
          message: "",
          status: false,
        },
        timeEnded: {
          message: "",
          status: false,
        },
        checkTime: {
          message: "",
          status: false,
        },
      },
      messages: {
        REQUIRED: "không được để trống",
        MAXLENGTH: "Chỉ cho phép độ dài kí tự từ 1-100",
        DATEOVER: "phải trong quá khứ ",
        SAMETIME: "Không được chọn thời gian giống nhau ",
        UNDER: "thời gian kết thúc không được nhỏ hơn thời gain bắt đầu",
        CompanyMIN: "Phải có tối thiểu 1 công ty"
      },
      listPosition: UserPosition,
      company: ListCompany,
    };
  },
  methods: {
    removeCompany(id) {
      if (this.formExperiences.length === 1) {
        this.error.company = {
          status: true,
          message: this.messages.CompanyMIN
        };
      } else this.formExperiences = this.formExperiences.filter((company) => company.id !== id);
      // let i = this.formExperiences.map((item) => item.id).indexOf(id);
      // if (this.formExperiences.length > 1) {
      //   this.formExperiences.splice(i, 1);
      //     this.error.company={
      //       status: true,
      //       message: this.messages.CompanyMIN
      //     }
       
      // } else {
      //   this.error.company.status = false;
      // }
    },
    addBlock() {
      this.formExperiences.push({
        id: shortid.generate(),
        exPosition: "",
        timeStared: "",
        timeEnded: "",
        exDesc: "",
      });
    },
    checkExPosition(item) {
      const exPosition = item.exPosition.length;
      if (!exPosition) {
        this.error.exPosition = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (exPosition > 1000) {
        this.error.exPosition = {
          status: true,
          message: this.messages.MAXLENGTH,
        };
      } else {
        this.error.exPosition.status = false;
      }
    },
    checkTimeStart(item) {
      const dateNow = Date.parse(new Date());
      const dateSelect = Date.parse(item.timeStared);
      if (!dateSelect || dateSelect === "NaN") {
        this.error.timeStared = {
          status: true,
          message: `Thời gian bắt đầu ${this.messages.REQUIRED}`,
        };
      } else if (dateNow <= dateSelect) {
        this.error.timeStared = {
          status: true,
          message: `Thời gian bắt đầu ${this.messages.DATEOVER}`,
        };
      } else {
        this.error.timeStared = false;
      }
    },
    checkTimeEnd(item) {
      const dateNow = Date.parse(new Date());
      const dateSelect = Date.parse(item.timeEnded);
      if (!dateSelect || dateSelect === "NaN") {
        this.error.timeEnded = {
          status: true,
          message: `Thời gian kết thúc ${this.messages.REQUIRED}`,
        };
      } else if (dateNow <= dateSelect) {
        this.error.timeEnded = {
          status: true,
          message: `Thời gian kết thúc ${this.messages.DATEOVER}`,
        };
      } else {
        this.error.timeEnded = false;
      }
    },
    checkDateExp(item) {
      this.checkTimeStart(item);
      this.checkTimeEnd(item);
      const timeStared = Date.parse(item.timeStared);
      const timeEnd = Date.parse(item.timeEnded);
      if (timeEnd < timeStared) {
        this.error.checkTime = {
          status: true,
          message: this.messages.UNDER,
        };
      } else if (timeEnd == timeStared) {
        this.error.checkTime = {
          status: true,
          message: this.messages.SAMETIME,
        };
      } else {
        this.error.checkTime = false;
      }
    },
    exDesc(item) {
      let desc = item.exDesc.length;
      if (!desc) {
        this.error.exDesc = {
          status: true,
          message: this.messages.REQUIRED,
        };
      } else if (desc >= 1000) {
        this.error.exDesc = {
          status: true,
          message: this.messages.MAXLENGTH,
        };
      } else this.error.exDesc.status = false;
    },
    nextStep() {
      for (let item of this.formExperiences) {
        this.checkExPosition(item);
        this.checkDateExp(item);
        this.exDesc(item);

        if (
          !this.error.company.status &&
          !this.error.exDesc.status &&
          !this.error.exPosition.status &&
          !this.error.checkTime.status
        ) {
        
        this.$emit("finishForm", this.formExperiences);
        this.$emit("nextStep");
      }}
    },
    preStep() {
      this.$emit("preStep");
    },
  },
  components: {
    DatePicker,
    SelectCom,
  },
};
</script>

<style lang="scss" scoped>
.step-2__block {
  margin: 10px 0px;
  border: 1px solid #ccc;
  padding: 25px;
  border-radius: 3px;
}
.company {
  display: flex;
  button {
    border: none;
    cursor: pointer;
  }
}
.inti {
  margin: 0px 20px;
}
</style>
