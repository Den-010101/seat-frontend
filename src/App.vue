<template>
  <div>
    <h2>座位列表</h2>
    <ul>
      <li v-for="seat in seats" :key="seat.FLOOR_SEAT_SEQ">
        座位編號：{{ seat.SEAT_NO }}，樓層：{{ seat.FLOOR_NO }}，員工：{{ seat.EMP_ID || '無人' }}
        
        <button v-if="!seat.EMP_ID" @click="openAssign(seat.FLOOR_SEAT_SEQ)">選取位置</button>
        <button v-if="seat.EMP_ID" @click="clearSeat(seat.FLOOR_SEAT_SEQ)">取消占用</button>

        <div v-if="selectedSeatId === seat.FLOOR_SEAT_SEQ">
          <select v-model="selectedEmpId">
            <option disabled value="">請選擇員工</option>
            <option v-for="emp in employees" :key="emp.EMP_ID" :value="emp.EMP_ID">
              {{ emp.EMP_ID }} - {{ emp.NAME }}
            </option>
          </select>
          <button @click="confirmAssign">指派</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      seats: [],
      employees: [],
      selectedSeatId: null,
      selectedEmpId: ''
    }
  },
  methods: {
    fetchSeats() {
      axios.get('http://localhost:8080/api/seats')
        .then(res => {
          this.seats = res.data;
        });
    },
    fetchEmployees() {
      axios.get('http://localhost:8080/api/seats/employees')
        .then(res => {
          this.employees = res.data;
        });
    },
    openAssign(seatId) {
      this.selectedSeatId = seatId;
      this.selectedEmpId = '';
    },
    confirmAssign() {
      if (!this.selectedEmpId) return alert("請選擇員工");

      axios.post('http://localhost:8080/api/seats/assign', null, {
        params: {
          empId: this.selectedEmpId,
          seatId: this.selectedSeatId
        }
      })
      .then(() => {
        alert("指派成功");
        this.selectedSeatId = null;
        this.fetchSeats();
      })
      .catch(err => {
        alert("指派失敗：" + (err.response?.data || "不明錯誤"));
      });
    },
    clearSeat(seatId) {
      if (!confirm("確定要取消占用嗎？")) return;

      axios.delete(`http://localhost:8080/api/seats/${seatId}`)
        .then(() => {
          alert("已取消占用");
          this.fetchSeats();
        });
    }
  },
  mounted() {
    this.fetchSeats();
    this.fetchEmployees();
  }
}

</script>
