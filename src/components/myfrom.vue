<template>
  <div class="myform" :style="formConfig.style">
    <el-form
      :model="ruleForm"
      :rules="rules"
      :ref="formName"
      class="demo-ruleForm"
    >
      <el-form-item
        v-for="(item, index) in formConfig.fromdata"
        :key="index"
        :style="item.style"
        :label="item.type != 'ity' ? item.label : ''"
        :prop="item.prop"
        :class="[
          { slotclass: item.type == 'slot' && item.class },
          { falseshowclass: item.show === true },
        ]"
        :required="item.required"
        v-show="item.show != false"
      >
       <!-- input框只读点击事件 -->
          <el-input
            v-if="
              (item.type == 'input' || item.type == 'textarea') && item.readonly
            "
            :type="item.type"
            :maxlength="item.max || ''"
            v-model="ruleForm[item.prop]"
            :clearable="true"
            :readonly="item.readonly || false"
            :placeholder="item.disabled ? '' : item.placeholder"
            :disabled="item.disabled || false"
            @click="item.click"
          >
            <template #suffix>{{ item.suffix }}</template>
          </el-input>
        <!-- input框blur事件 -->
        <el-input
          v-if="item.type == 'input' && item.blur == true"
          :maxlength="item.max || '20'"
          v-model="ruleForm[item.prop]"
          :clearable="true"
          :placeholder="item.disabled ? '' : item.placeholder"
          :disabled="item.disabled || false"
          :show-password="item.showpsd ? true : false"
          @blur="item.click(ruleForm[item.prop])"
        >
          <template #suffix>{{ item.suffix }}</template>
        </el-input>
        <!-- 正常input框 -->
        <el-input
          v-else-if="
            item.type == 'input' && item.readonly != true && item.blur != true
          "
          :maxlength="item.max || '20'"
          v-model="ruleForm[item.prop]"
          :clearable="true"
          :placeholder="item.disabled ? '' : item.placeholder"
          :disabled="item.disabled || false"
          :show-password="item.showpsd ? true : false"
        >
          <template #suffix>{{ item.suffix }}</template>
        </el-input>
        <div v-else-if="item.type == 'ity'"></div>

        <!-- input框带搜索 -->
        <el-autocomplete
          v-else-if="item.type == 'autocomplete'"
          class="inline-input"
          v-model="ruleForm[item.prop]"
          :fetch-suggestions="item.querySearch"
          :placeholder="item.placeholder"
          :trigger-on-focus="false"
          :disabled="item.disabled || false"
          :clearable="true"
          :show-password="item.showpsd ? true : false"
          @select="item.handleSelect($event)"
        ></el-autocomplete>
        <!-- select框 -->
        <el-select
          v-else-if="item.type == 'select'"
          v-model="ruleForm[item.prop]"
          :multiple="item.multiple"
          :filterable="item.filterable || false"
          :placeholder="item.disabled ? '' : item.placeholder"
          :disabled="item.disabled || false"
          @change="item.click(ruleForm[item.prop])"
          :clearable="true"
          :value-key="item.value"
          :collapse-tags="item.tags"
        >
          <div v-if="item.valueType">
            <el-option
              v-for="(it, index) in item.options"
              :label="it.label"
              :multiple="it.multiple || false"
              :filterable="it.filterable || false"
              :key="it.value + index"
              :value="it"
            ></el-option>
          </div>
          <div v-else>
            <el-option
              v-for="(it, ind) in item.options"
              :label="it.label"
              :multiple="it.multiple || false"
              :filterable="it.filterable || false"
              :key="it.value + '' + index + ind"
              :value="it.value"
            ></el-option>
          </div>
        </el-select>

        <!-- 多行文本框 -->
        <el-input
          v-else-if="item.type == 'textarea'"
          :type="item.type"
          :maxlength="item.max || '300'"
          v-model="ruleForm[item.prop]"
          :placeholder="item.disabled ? '' : item.placeholder"
          :disabled="item.disabled || false"
          @focus="item.click([item.prop])"
        ></el-input>
        <!-- Switch 开关 -->
        <el-switch
          v-else-if="item.type == 'switch'"
          :active-color="item.selectColor"
          :inactive-color="item.noSelectColor"
          :disabled="item.disabled || false"
          :active-value="item.selectvalue"
          :inactive-value="item.noSelectvalue"
          :active-text="item.text"
          v-model="ruleForm[item.prop]"
          @change="item.click"
        ></el-switch>
        <!-- 单选 -->
        <el-radio-group
          v-else-if="item.type == 'radio'"
          v-model="ruleForm[item.prop]"
          :disabled="item.disabled || false"
          @change="item.click"
        >
          <el-radio
            v-for="itm in item.options"
            :label="itm.value"
            :key="itm.value"
            >{{ itm.label }}</el-radio
          >
        </el-radio-group>

        <!-- 多选 -->
        <el-checkbox-group
          v-model="ruleForm[item.prop]"
          v-else-if="item.type == 'checkbox'"
          :disabled="item.disabled || false"
          @change="item.click"
        >
          <el-checkbox
            :label="itm.value"
            v-for="itm in item.options"
            :key="itm.value"
            >{{ itm.label }}</el-checkbox
          >
        </el-checkbox-group>
        <!-- 照片 -->
        <div v-else-if="item.type == 'Photo'">
          <el-image
            :src="item.src"
            fit="cover"
            :preview-src-list="item.srcList"
            v-if="item.src"
          ></el-image>
          <span v-else style="color: red">暂未上传图片</span>
        </div>

        <!-- 视频播放 -->
        <div v-else-if="item.type == 'video'">
          <video
            :width="item.width"
            :height="item.height"
            controls
            v-if="item.src != ''"
          >
            <source :src="item.src" type="video/mp4" />
            <source :src="item.src" type="video/ogg" />
            <source :src="item.src" type="video/webm" />
            <object :data="item.src" width="100%" height="10%">
              <embed :src="item.src" width="100%" height="100%" />
            </object>
          </video>
          <span v-else style="color: red">暂未上传视频</span>
        </div>
        <!-- 级联选择器 -->
        <el-cascader
          v-else-if="item.type == 'cascader'"
          v-model="ruleForm[item.prop]"
          clearable
          :key="keyValue"
          :options="item.options"
          :props="item.props"
          :show-all-levels="item.levels"
          :placeholder="item.placeholder"
          :filterable="item.filterable"
          :collapse-tags="item.collapse ? true : false"
          @change="handleChange"
        >
          <template v-slot:default="{ data }">
            <span>{{ data.label }}</span>
          </template>
        </el-cascader>

        <el-upload
          v-else-if="item.type == 'file'"
          :ref="item.prop"
          :required="item.required"
          :action="action"
          :headers="importHeaders"
          :accept="
            item.accept
              ? item.accept
              : 'image/gif, image/jpeg,image/png,audio/mp3,audio/mpeg'
          "
          class="upload-demo"
          :auto-upload="true"
          :on-success="
            (file, fileList) => handleAvatarSuccess(file, fileList, item.prop)
          "
          :before-upload="
            (file) =>
              beforeAvatarUpload(file, index, item.accept, item.min, item.max)
          "
          :on-remove="
            (file, fileList) => handleRemove(file, fileList, index, item.prop)
          "
          :before-remove="
            (file, fileList) => beforeRemove(file, fileList, index, item.prop)
          "
          multiple
          :limit="item.limit"
          :on-exceed="
            (files, fileList) =>
              handleExceed(files, fileList, index, item.label, item.limit)
          "
          :file-list="item.fileList"
        >
          <el-button type="primary">点击上传</el-button>
          <template #tip>
            <div class="el-upload__tip">
              {{ item.prompting }}
            </div>
          </template>
        </el-upload>

        <!-- 自定义 -->
        <template v-else-if="item.type === 'slot'">
          <slot :name="item.slot" />
        </template>
        <!-- 时间 -->
        <div v-else-if="item.type == 'doubleTime'" class="doubletime">
          <el-date-picker
            :value-format="
              item.start.dateFormate
                ? item.start.dateFormate
                : 'yyyy-MM-dd HH:mm:ss'
            "
            v-model="ruleForm[item.start.prop]"
            :type="item.datatype"
            :disabled="item.start.disabled"
            clearable
            :picker-options="item.start.pickerOptions"
            :placeholder="item.start.disabled ? '' : item.start.placeholder"
          ></el-date-picker>
          <span>-</span>
          <el-date-picker
            :type="item.datatype"
            v-model="ruleForm[item.end.prop]"
            :disabled="item.end.disabled"
            clearable
            :picker-options="item.end.pickerOptions"
            :value-format="
              item.end.dateFormate
                ? item.end.dateFormate
                : 'yyyy-MM-dd HH:mm:ss'
            "
            :placeholder="item.end.disabled ? '' : item.end.placeholder"
          ></el-date-picker>
        </div>
        <el-date-picker
          v-else
          :type="item.type"
          v-model="ruleForm[item.prop]"
          :disabled="item.disabled"
          :picker-options="item.option"
          :placeholder="item.disabled ? '' : item.placeholder"
          :value-format="
            item.dateFormate ? item.dateFormate : 'yyyy-MM-dd HH:mm:ss'
          "
          @change="
            item.handlePickerDate
              ? item.handlePickerDate(ruleForm[item.prop])
              : function () {}
          "
        ></el-date-picker>
      </el-form-item>
    </el-form>
    <div class="fromfoot" v-if="formConfig.footshow">
      <el-button
        v-for="(list, ind) in formConfig.footer"
        :key="ind"
        :type="list.type"
        :size="list.size"
        @click="list.click"
        >{{ list.name }}</el-button
      >
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, ref } from "vue";
import { ElMessage, ElMessageBox } from "element-plus";
export default defineComponent({
  props: {
    formName: {
      type: String,
      required: true,
    },
    formConfig: {
      type: Object,
      required: true,
    },
    ruleForm: {
      type: Object,
      required: true,
    },
    rules: {
      type: Object,
      required: true,
    },
  },
  setup(props, context) {
    const keyValue = ref(0);
    const action = ref("");
    return {
      keyValue,
      action,
      importHeaders: { token: sessionStorage.getItem("token") },
      handleAvatarSuccess,
      beforeAvatarUpload,
      handleRemove,
      beforeRemove,
      handleExceed,
      handleChange,
    };
  },
});
const ruleForm = ref("ruleForm");
interface RawFile {
  name: string;
  url: string;
}
const fileList = ref<RawFile[]>();
const handleAvatarSuccess = (res, file, prop) => {
  ruleForm[prop] = res.data.fileId;
};
const beforeAvatarUpload = (file, index, accept, min, max) => {
  const isLt2M = file.size / 1024 / 1024 < 3;
  const isaudio = file.size / 1024 / 1024 < 20;
  if (file.type == "audio/mpeg") {
    if (!isaudio) {
      ElMessage.error("上传mp3文件大小不能超过20MB");
    }
  } else {
    if (min && max) {
      if (file.size < min || file.size > max) {
        ElMessage.error("上传图片大小需大于10KB小于200KB!");
        return false;
      } else {
        return true;
      }
    } else {
      if (!isLt2M) {
        ElMessage.error("上传头像图片大小不能超过 2MB!");
      }
      return isLt2M;
    }
  }
};
// 删除文件
const handleRemove = (file, fileList, index, prop) => {
  if (fileList.length > 0) {
    let file = {
      file: fileList[0].raw,
    };
    // this.$api.tsutils.upload(file).then(res => {
    //   if (res.status == 200) {
    //     ruleForm[prop] = res.data.fileId;
    //   }
    // });
  } else {
    ruleForm[prop] = "";
  }
};
const beforeRemove = (file, fileList, index, prop) => {
  if (file && file.status === "success") {
    return ElMessageBox.confirm(`确定移除 ${file.name} ?`, "");
  }
};
//超出限制
const handleExceed = (files, fileList, index, label, limit) => {
  ElMessageBox({
    showClose: true,
    message: `${label}限制选择 ${limit}个文件，本次选择了 ${
      files.length
    } 个文件，共选择了 ${files.length + fileList.length} 个文件`,
    type: 'warning',
  });
};
//级联选择
const handleChange = (val) => {};
</script>

<style lang="scss">
.myform {
  .el-form {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    .is-required {
      // max-width: 580px;
      .el-form-item__label {
        line-height: 42px;
        width: 110px;
        text-align: left;
        padding-left: 0px;
      }
    }
  }

  .el-form-item {
    // max-width: 580px;
    display: flex;
    flex-wrap: nowrap;
    align-items: center;
    .el-form-item__error {
      padding-left: 12px;
    }
    .el-form-item__label {
      line-height: 42px;
      width: 110px;
      text-align: left;
      padding-left: 12px;
    }
    .el-form-item__content {
      line-height: 42px;
      width: 460px;
      text-align: left;
      .el-select {
        .is-disabled {
          .el-input__suffix {
            opacity: 0;
          }
        }
      }

      .doubletime {
        display: flex;
        align-items: center;
      }
      .is-disabled.is-checked {
        .el-radio__inner {
          display: none;
        }
      }
      .is-disabled {
        .el-input__inner {
          background: #fff;
          color: #676767;
          border: none;
        }
      }
      .el-input__inner {
        height: 42px;
      }
      .el-radio-group {
        .el-radio__inner:hover {
          border-color: #676767;
        }
        .el-radio__input.is-disabled.is-checked .el-radio__inner::after {
          background-color: #fff;
        }
        .is-disabled {
          display: none;
        }
        .is-checked {
          display: flex;
        }
        .el-radio__input.is-checked + .el-radio__label {
          color: #676767;
        }
        .el-radio__input.is-checked {
          .el-radio__inner {
            border-color: #676767;
            background: #676767;
          }
        }
      }
    }
  }
  .fromfoot {
    margin-top: 80px;
    width: 460px;
    margin-left: 120px;
    padding: 0 20px;
    display: flex;
    justify-content: space-between;
    .el-button {
      width: 166px;
    }
    .el-button--primary {
      background: #3860f4;
    }
  }
}
/*修改滚动条样式
  :-webkit-scrollbar 滚动条整体部分
::-webkit-scrollbar-thumb  滚动条里面的小方块，能向上向下移动（或往左往右移动，取决于是垂直滚动条还是水平滚动条）
::-webkit-scrollbar-track  滚动条的轨道（里面装有Thumb）
::-webkit-scrollbar-button 滚动条的轨道的两端按钮，允许通过点击微调小方块的位置。
::-webkit-scrollbar-track-piece 内层轨道，滚动条中间部分（除去）
::-webkit-scrollbar-corner 边角，即两个滚动条的交汇处
::-webkit-resizer 两个滚动条的交汇处上用于通过拖动调整元素大小的小控件
  */
div::-webkit-scrollbar {
  width: 8px;
  height: 10px;
}
div::-webkit-scrollbar-track {
  background: rgb(239, 239, 239);
  border-radius: 2px;
}
div::-webkit-scrollbar-thumb {
  background: rgba(191, 191, 191, 0.4);
  border-radius: 20px;
}
div::-webkit-scrollbar-thumb:hover {
  background: #333;
}
.el-radio__input.is-disabled.is-checked .el-radio__inner::after {
  background-color: #3860f4;
}
.el-radio__input.is-checked .el-radio__inner {
  border-color: #3860f4;
}
</style>