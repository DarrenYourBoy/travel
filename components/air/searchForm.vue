<template>
  <div class="search-form">
    <!-- 头部tab切换 -->
    <el-row type="flex" class="search-tab">
      <span
        v-for="(item, index) in tabs"
        :key="index"
        @click="handleSearchTab(item, index)"
        :class="{active: index === currentTab}"
      >
        <i :class="item.icon"></i>
        {{item.name}}
      </span>
    </el-row>

    <el-form class="search-form-content" ref="form" label-width="80px">
      <el-form-item label="出发城市">
        <!-- fetch-suggestions: 类似于input方法，每次输入框值发生变化时候回触发 -->
        <!-- select：选中下拉列表中的值的时候触发的触发  -->
        <el-autocomplete
          :fetch-suggestions="queryDepartSearch"
          placeholder="请搜索出发城市"
          @select="handleDepartSelect"
          class="el-autocomplete"
          v-model="form.departCity"
          @blur="defaultBlur('depart')"
        ></el-autocomplete>
      </el-form-item>

      <el-form-item label="到达城市">
        <el-autocomplete
          :fetch-suggestions="queryDestSearch"
          placeholder="请搜索到达城市"
          @select="handleDestSelect"
          v-model="form.destCity"
          class="el-autocomplete"
          @blur="defaultBlur('dest')"
        ></el-autocomplete>
      </el-form-item>

      <el-form-item label="出发时间">
        <!-- change 用户确认选择日期时触发 -->
        <el-date-picker
          type="date"
          placeholder="请选择日期"
          style="width: 100%;"
          @change="handleDate"
          v-model="form.departDate"
        ></el-date-picker>
      </el-form-item>

      <el-form-item label>
        <el-button style="width:100%;" type="primary" icon="el-icon-search" @click="handleSubmit">搜索</el-button>
      </el-form-item>
      <div class="reverse">
        <span @click="handleReverse">换</span>
      </div>
    </el-form>
  </div>
</template>

<script>
import moment from "moment";
import { async } from "q";
export default {
  data() {
    return {
      tabs: [
        { icon: "iconfont icondancheng", name: "单程" },
        { icon: "iconfont iconshuangxiang", name: "往返" }
      ],
      currentTab: 0,
      // 最终表单要提交的属性
      form: {
        departCity: "", // 出发城市
        departCode: "", // 出发城市代码
        destCity: "", // 到达城市
        destCode: "", // 到达城市代码
        departDate: "" // 日期字符串
      },
      cities: []
    };
  },
  methods: {
    // tab切换时触发
    handleSearchTab(item, index) {},

    // 出发城市输入框值发生变化时候会触发
    // value：输入框的值
    // cb:回调函数，必须要调用，调用时候必须要传递一个数组的参数，
    // 数组中的元素必须是一个对象，对象中必须要有value属性
    async queryDepartSearch(value, cb) {
      // 输入框为空时不弹出选择框
      value = value.replace(/\s*/g,"");
      if (!value) {
        cb([]);
        return;
      }
      const res = await this.$axios({
        url: "/airs/city?name=" + value
      });
      const { data } = res.data;
      const newData = data.map(v => {
        //去掉v.name后的‘市’子，并创建value属性接收
        v.value = v.name.replace("市", "");
        return v;
      });
      //在弹出选择框后，当用户未在选择框进行选择时，默认给用户选择第一项
      this.cities = newData;
      cb(newData);
    },
    // 目标城市输入框获得焦点时触发
    // value 是选中的值，cb是回调函数，接收要展示的列表
    async queryDestSearch(value, cb) {
      this.queryDepartSearch(value, cb);
    },
    defaultBlur(type) {
      if (this.cities.length === 0) return;
      if (this.cities.length > 0) {
        this.form[type + "City"] = this.cities[0].value;
        this.form[type + "Code"] = this.cities[0].sort;
      }
    },
    // 出发城市下拉选择时触发
    handleDepartSelect(item) {
      this.form.departCity = item.value;
      this.form.departCode = item.sort;
    },
    // 目标城市下拉选择时触发
    handleDestSelect(item) {
      this.form.destCity = item.value;
      this.form.destCode = item.sort;
    },
    // 确认选择日期时触发
    handleDate(value) {
      this.form.departDate = moment(value).format("YYYY-MM-DD");
    },
    // 触发和目标城市切换时触发
    handleReverse() {
      const { departCity, departCode, destCity, destCode } = this.form;
      this.form.departCity = destCity;
      this.form.destCity = departCity;
      this.form.departCode = destCode;
      this.form.destCode = departCode;
    },
    // 提交表单是触发
    async handleSubmit() {
      const {
        departCity,
        departCode,
        destCity,
        destCode,
        departDate
      } = this.form;
      this.$router.push(
        `/air/flights?departCity=${departCity}&departCode=${departCode}&destCity${destCity}&destCode=${destCode}&departDate=${departDate}`
      );
    }
  },
  mounted() {}
};
</script>

<style scoped lang="less">
.search-form {
  border: 1px #ddd solid;
  border-top: none;
  width: 360px;
  height: 350px;
  box-sizing: border-box;
}
.search-tab {
  span {
    display: block;
    flex: 1;
    text-align: center;
    height: 48px;
    line-height: 42px;
    box-sizing: border-box;
    border-top: 3px #eee solid;
    background: #eee;
  }
  .active {
    border-top-color: orange;
    background: #fff;
  }
  i {
    margin-right: 5px;
    font-size: 18px;
    &:first-child {
      font-size: 16px;
    }
  }
}
.search-form-content {
  padding: 15px 50px 15px 15px;
  position: relative;
  .el-autocomplete {
    width: 100%;
  }
}
.reverse {
  position: absolute;
  top: 35px;
  right: 15px;
  &:after,
  &:before {
    display: block;
    content: "";
    position: absolute;
    left: -35px;
    width: 25px;
    height: 1px;
    background: #ccc;
  }
  &:after {
    top: 0;
  }
  &:before {
    top: 60px;
  }
  span {
    display: block;
    position: absolute;
    top: 20px;
    right: 0;
    font-size: 12px;
    background: #999;
    color: #fff;
    width: 20px;
    height: 20px;
    line-height: 18px;
    text-align: center;
    border-radius: 2px;
    cursor: pointer;
    &:after,
    &:before {
      display: block;
      content: "";
      position: absolute;
      left: 10px;
      width: 1px;
      height: 20px;
      background: #ccc;
    }
    &:after {
      top: -20px;
    }
    &:before {
      top: 20px;
    }
  }
}
</style>