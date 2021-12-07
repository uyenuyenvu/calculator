<template>
  <div class="calculator">
    <div class="cal1 cal">
      <p class="label">Máy tính</p>
      <p>1/ Chọn loại công việc</p>
      <el-radio v-model="typeJob" value="1" label="1">Joint sealing</el-radio>
      <br>
      <el-radio v-model="typeJob" value="2" label="2">Device SMDS</el-radio>
      <br>
      <el-radio v-model="typeJob" value="3" label="3">Protection and restoration of the coating</el-radio>
      <p class="label">2/ Thiết lập bộ lọc</p>
      <p>Chọn một khu vực khí hậu đường bộ</p>
      <el-select v-model="localSelected" placeholder="Select">
        <el-option
            v-for="item in local"
            :key="item.key"
            :label="item.key"
            :value="item.key">
        </el-option>
      </el-select>
      <p>Chọn một chất bịt kín</p>
      <el-select v-model="materialSelected" placeholder="Select">
        <el-option
            v-for="item in materialByLocal"
            :key="item.name"
            :label="getTextMaterial(item.name, item.m)"
            :value="item.m">
        </el-option>
      </el-select>
      <p class="label">3/ Nhập dữ liệu</p>
      <div class="round" v-for="(round, index) in rounds" :key="index">
        <el-button type="danger" icon="el-icon-delete" circle class="buttonDelete" v-if="index!==0"
                   @click="deleteRound(index)"></el-button>
        <p>Đường {{ index + 1 }}</p>
        <el-row :gutter="0">
          <el-col :sm="12">
            <p>Chiều dài(m)</p>
            <el-input placeholder="Please input" v-model="round.l"></el-input>
          </el-col>
          <el-col :sm="12">
            <p>Chiều rộng(mm)</p>
            <el-input placeholder="Please input" v-model="round.b"></el-input>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :sm="12">
            <p>Chiều sâu(mm)</p>
            <el-input placeholder="Please input" v-model="round.h"></el-input>
          </el-col>
        </el-row>
        <p v-if="rounds[index].error && rounds[index].error.length > 0" class="error">{{ rounds[index].error }}</p>
      </div>

      <el-row :gutter="0">
        <el-col :sm="24" style="text-align: right">
          <el-button type="primary" plain @click="addRound">Thêm đường</el-button>
        </el-col>
      </el-row>
      <el-row :gutter="0">
        <el-col :sm="12">
          <el-button type="info" plain @click="submit1()">Tính</el-button>
        </el-col>
      </el-row>
    </div>
    <div class="cal2 cal">
      <p class="label">Máy tính</p>
      <p>Kết quả tính toán</p>
      <p>Vùng khí hậu đường bộ</p>
      <el-input v-model="localSelected"></el-input>
      <p>Chọn một chất bịt kín</p>
      <el-input :value="getMaterial()"></el-input>
      <p>Những sản phẩm tương tự</p>
      <el-input :value="'Sơn lót polyme “brit”'"></el-input>
      <el-input :value="'Dây niêm phong chịu nhiệt'"></el-input>
      <div class="table">
        <table cellspacing="0">
          <tr>
            <td style="width: 70px"></td>
            <td v-for="(round,index) in rounds" :key="index" style="width: 80px">Đường {{index+1}}</td>
            <td>Tổng</td>
          </tr>
          <tr>
            <td>Dây 15mm, m</td>
            <td v-for="(round,index) in rounds" :key="index" style="width: 80px">2</td>
            <td>{{ t1 }}</td>
          </tr>
          <tr>
            <td>Khối lượng chất bịt kín, kg</td>
            <td v-for="(round,index) in rounds" :key="index" style="width: 80px">0.03</td>
            <td>{{ t2 }}</td>
          </tr>
          <tr>
            <td>Khối lượng của sơn lót, kg</td>
            <td v-for="(round,index) in rounds" :key="index" style="width: 80px">0.13</td>
            <td>{{ t3 }}</td>
          </tr>
        </table>
      </div>
      <el-row :gutter="0" style="padding-top: 20px">
        <el-col :sm="12" style="text-align: center;">
          <el-button type="primary" plain>Thay đổi</el-button>
        </el-col>
        <el-col :sm="12" style="text-align: center">
          <el-button type="primary" plain>Tính toán</el-button>
        </el-col>
      </el-row>
    </div>
    <div class="cal3 cal">
      <p class="label">Máy tính</p>
      <p>1/ Chọn loại công việc</p>
      <el-radio v-model="typeJob" value="1" label="1">Joint sealing</el-radio>
      <br>
      <el-radio v-model="typeJob" value="2" label="2">Device SMDS</el-radio>
      <br>
      <el-radio v-model="typeJob" value="3" label="3">Protection and restoration of the coating</el-radio>
      <p>2/ Thiết lập bộ lọc</p>
      <p>Chọn loại chất</p>
      <el-input value="Xi măng nhựa đường"></el-input>
      <p>Chọn vật liệu để xử lý bề mặt</p>
      <el-input value="DVC R(V)"></el-input>
      <el-row :gutter="0">
        <el-col :sm="12">
          <el-radio v-model="typeJob2" value="1" label="1">Tiêu thụ trung bình</el-radio>
        </el-col>
        <el-col :sm="12">
          <el-radio v-model="typeJob2" value="2" label="2">Thay đổi thủ công</el-radio>
        </el-col>
        <el-col :sm="24" style="margin-top: 10px">
          <el-input :value="1.3"></el-input>
        </el-col>
      </el-row>
      <p>3/ Nhập dữ liệu diện tích bề mặt được xử lý <br>(m<sup>2</sup>)</p>
      <el-input value=""></el-input>

      <el-row :gutter="0" style="margin-top: 20px; text-align: center">
        <el-col :sm="24">
          <el-button type="info" plain>Tính</el-button>
        </el-col>
      </el-row>
    </div>
    <div class="cal4 cal">
      <p class="label">Máy tính</p>
      <p>1/ Chọn loại công việc</p>
      <el-radio v-model="typeJob" value="1" label="1">Joint sealing</el-radio>
      <br>
      <el-radio v-model="typeJob" value="2" label="2">Device SMDS</el-radio>
      <br>
      <el-radio v-model="typeJob" value="3" label="3">Protection and restoration of the coating</el-radio>
      <p>2/ Thiết lập bộ lọc</p>
      <p>Chọn loại chất</p>
      <el-input value="Bê tông xi măng"></el-input>
      <p>Chọn vật liệu để xử lý bề mặt</p>
      <el-input value="Thấm nước"></el-input>
      <el-row :gutter="0">
        <el-col :sm="12">
          <el-radio v-model="typeJob2" value="1" label="1">Tiêu thụ trung bình</el-radio>
        </el-col>
        <el-col :sm="12">
          <el-radio v-model="typeJob2" value="2" label="2">Thay đổi thủ công</el-radio>
        </el-col>
        <el-col :sm="24" style="margin-top: 10px">
          <el-input :value="0.2"></el-input>
        </el-col>
      </el-row>
      <p>3/ Nhập dữ liệu diện tích bề mặt được xử lý <br> (m<sup>2</sup>)</p>
      <el-input value=""></el-input>
      <el-row :gutter="0" style="margin-top: 20px; text-align: center">
        <el-col :sm="24">
          <el-button type="info" plain>Tính</el-button>
        </el-col>
      </el-row>
    </div>
    <div class="cal5 cal">
      <p class="label">Máy tính</p>
      <p>Kết quả tính toán</p>
      <p>Vùng khí hậu đường bộ</p>
      <el-input v-model="localSelected"></el-input>
      <p>Chọn một chất bịt kín</p>
      <el-input :value="getMaterial()"></el-input>
      <div class="table">
        <table cellspacing="0">
          <tr>
            <td></td>
            <td>Đường 1</td>
            <td>Đường 2</td>
            <td>Tổng</td>
          </tr>
          <tr>
            <td>Khối lượng đá nghiền, 10-20, kg</td>
            <td>0.4</td>
            <td>0.4</td>
            <td>1</td>
          </tr>
          <tr>
            <td>Khối lượng đá nghiền, 5-10, kg</td>
            <td>36</td>
            <td>36</td>
            <td>72</td>
          </tr>
          <tr>
            <td>Khối lượng đá nghiền, 3-5, kg</td>
            <td>0.6</td>
            <td>0.6</td>
            <td>2</td>
          </tr>
          <tr>
            <td>Khối lượng của sơn lót, kg</td>
            <td>0.02</td>
            <td>0.02</td>
            <td>1</td>
          </tr>
          <tr>
            <td>Khối lượng mastic, kg</td>
            <td>0.53</td>
            <td>0.53</td>
            <td>2</td>
          </tr>
        </table>
      </div>
      <el-row :gutter="0" style="padding-top: 20px">
        <el-col :sm="12" style="text-align: center;">
          <el-button type="primary" plain>Thay đổi</el-button>
        </el-col>
        <el-col :sm="12" style="text-align: center">
          <el-button type="primary" plain>Tính toán</el-button>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  name: "Calculator",
  data() {
    return {
      typeJob: 1,
      typeJob2: 1,
      localSelected: '',
      materialByLocal: [],
      materialSelected: '',
      local: [
        {
          key: '1',
          material: [
            {
              name: '2',
              m: 3
            }
          ]
        },
        {
          key: 'I',
          material: [
            {
              name: 'BP-G50',
              m: 1.29
            },
            {
              name: 'NORD',
              m: 1.12
            }
          ]
        },
        {
          key: 'II',
          material: [
            {
              name: 'BP-G50',
              m: 1.29
            },
            {
              name: 'NORD',
              m: 1.12
            }
          ]
        },
        {
          key: 'III',
          material: [
            {
              name: 'BP-G35',
              m: 1.21
            },
            {
              name: 'Arctic-3',
              m: 1.09
            }
          ]
        },
        {
          key: 'IV',
          material: [
            {
              name: 'BP-G35',
              m: 1.21
            },
            {
              name: 'Arctic-3',
              m: 1.09
            }
          ]
        },
        {
          key: 'V',
          material: [
            {
              name: 'BP-G25',
              m: 1.20
            }
          ]
        },
      ],
      material: 'BP-G50 (tỉ trọng g/cm3 = 1.29)',
      rounds: [
        {
          id: new Date().getTime(),
          l: '',
          b: '',
          h: ''
        }
      ],
      tableA: [
        {
          start: 0,
          end: 4,
          value: 2
        },
        {
          start: 5,
          end: 8,
          value: 10
        },
        {
          start: 9,
          end: 10,
          value: 13
        },
        {
          start: 11,
          end: 12,
          value: 15
        },
        {
          start: 13,
          end: 15,
          value: 20
        },
        {
          start: 16,
          end: 18,
          value: 22
        },
        {
          start: 19,
          end: 20,
          value: 25
        },
        {
          start: 21,
          end: 28,
          value: 30
        },
        {
          start: 29,
          end: 35,
          value: 38
        },
      ],
      t1: 0,
      t2: 0,
      t3: 0
    }
  },

  methods: {
    addRound() {
      this.rounds.push({
        id: new Date().getTime(),
        l: '',
        b: '',
        h: ''
      })
    },
    deleteRound(index) {
      this.rounds = _.filter(this.rounds, function (item, i) {
        return i !== index
      })
    },
    getTextMaterial(name, m) {
      return name + ' (tỉ trọng g/cm3 = ' + m + ')'
    },
    getMaterial() {
      let m = this.materialSelected
      let mar = _.filter(this.materialByLocal, (item) => {
        return item.m === m
      })
      if (mar.length > 0) {
        return mar[0].name + ' (tỉ trọng g/cm3 = ' + mar[0].m + ')'
      }
      return ''
    },
    submit1() {
      let t1 = 0
      let t2 = 0
      let t3 = 0
      let mate = this.materialSelected
      this.rounds.forEach((item) => {
        let value2 = (parseFloat(item.l) + parseFloat(item.b) + parseFloat(item.h)) * parseFloat(mate) / 1000
        console.log(parseFloat(mate))
        t2 += value2
        let value3 = 0.25 * (parseFloat(item.l) * parseFloat(item.b) + 2 * parseFloat(item.l) * parseFloat(item.h))
        t3 += value3
      })
      this.t1 = t1
      this.t2 = parseInt(t2 * 10000) / 10000
      this.t3 = parseInt(t3 * 10000) / 10000
    }
  },
  watch: {
    localSelected(val) {
      let material = _.filter(this.local, function (item) {
        return item.key == val
      })
      this.materialByLocal = material.length > 0 ? material[0].material : []
    },
    rounds: {
      handler: function () {
        this.rounds.forEach((item) => {
          if (item.h >= item.b && item.h > 0) {
            item.error = 'Chiều rộng phải lớn hơn độ sâu !'
          } else {
            item.error = ''
          }

        })
      },
      deep: true
    }
  }
}
</script>

<style scoped>
.calculator {
  display: flex;
  width: max-content;
}

.cal {
  width: max-content;
  margin: 0.5%;
  border: 1px solid black;
  padding: 10px;
  min-width: 300px;
}

.el-select {
  width: 100%;
}

.el-input {
  margin-bottom: 10px;
}

table {
  width: 100%;
  margin: 0 auto;
}

td {
  border: 1px solid black;
  padding: 5px;
  text-align: center;
}

.round {
  position: relative;
}

.buttonDelete {
  position: absolute;
  top: 0;
  right: 0;
}

.error {
  color: red;
  font-style: italic;
}
</style>