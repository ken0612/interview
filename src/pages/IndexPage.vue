<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model.number="tempData.age" label="年齡" />
        <q-btn @click="addPersonData" color="primary" class="q-mt-md"
          >新增</q-btn
        >
      </div>

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <input v-show="isEdit" v-model="col.value" />
              <div v-show="!isEdit">{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :disable="btn.disable"
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref } from 'vue';
interface btnType {
  label: string;
  icon: string;
  status: string;
}
const personId = ref(1);
const blockData = ref([
  {
    id: personId.value,
    name: 'test',
    age: 25,
    isEdit: false,
  },
]);
const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
const tableButtons = ref([
  {
    label: '儲存',
    icon: 'save',
    status: 'save',
    disable: true,
  },
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
    disable: false,
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
    disable: false,
  },
]);

const isEdit = ref(false);
const tempData = ref({
  name: '',
  age: '',
});
function handleClickOption(btn, data) {
  // ...
  // axios
  //   .get('/crudTest')
  //   .then((res) => {
  //     console.log(res);
  //   })
  //   .catch((err) => {
  //     console.log(err.message);
  //   });

  console.log(btn, data);
  console.log(btn.label);
  if (btn.label === '編輯') {
    isEdit.value = true;
    tableButtons.value[1].disable = true;
    tableButtons.value[0].disable = false;
  } else {
    tableButtons.value[0].disable = true;
    tableButtons.value[1].disable = false;
    isEdit.value = false;
    for (let i = 0; i < blockData.value.length; i++) {
      if (blockData.value[i] == data) {
        blockData.value[i] = data;
      }
    }
  }

  if (btn.label === '刪除') {
    const confirmResult = confirm('確定要刪除嗎？');
    console.log(confirmResult);
    if (confirmResult) {
      for (let i = 0; i < blockData.value.length; i++) {
        if (data.id === blockData.value[i].id) {
          blockData.value.splice(i, 1);
        }
      }
    }
  }
}

function addPersonData() {
  personId.value++;
  const vali = /^(100|[1-9]\d?)$/;
  if (!vali.test(tempData.value.age)) {
    alert('年齡只能輸入整數');
    return;
  }
  if (tempData.value.name == '' || tempData.value.name == null) {
    alert('請填寫姓名！');
    return;
  }
  let newData = {
    id: personId.value,
    name: tempData.value.name,
    age: parseInt(tempData.value.age, 10),
    isEdit: false,
  };
  blockData.value.unshift(newData);
  (tempData.value.name = ''), (tempData.value.age = '');
}
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
