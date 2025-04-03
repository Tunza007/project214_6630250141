<template>
    <div class="card shadow">
      <div class="card-body">
        <h4 class="text-success fw-bold mb-4">ข้อมูลนิสิต</h4>
  
        <div v-if="!editing">
          <p><strong>ชื่อ-นามสกุล:</strong> {{ student.name }}</p>
          <p><strong>รหัสนิสิต:</strong> {{ student.id }}</p>
          <p><strong>สาขา:</strong> {{ student.major }}</p>
          <p><strong>โรงเรียนเดิม:</strong> {{ student.school }}</p>
          <button class="btn btn-success" @click="editing = true">แก้ไข</button>
        </div>
  
        <div v-else>
          <input class="form-control mb-2" v-model="edited.name" placeholder="ชื่อ-นามสกุล" />
          <input class="form-control mb-2" v-model="edited.id" placeholder="รหัสนิสิต" />
          <input class="form-control mb-2" v-model="edited.major" placeholder="สาขา" />
          <input class="form-control mb-2" v-model="edited.school" placeholder="โรงเรียนเดิม" />
          <button class="btn btn-primary me-2" @click="save">บันทึก</button>
          <button class="btn btn-secondary" @click="cancel">ยกเลิก</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'StudentInfo',
    props: ['student'],
    data() {
      return {
        editing: false,
        edited: { ...this.student }
      }
    },
    watch: {
      student(newVal) {
        this.edited = { ...newVal }
      }
    },
    methods: {
      save() {
        this.editing = false
        this.$emit('update-student', { ...this.edited })
  
        // ✅ Save to localStorage
        localStorage.setItem('student', JSON.stringify(this.edited))
      },
      cancel() {
        this.editing = false
        this.edited = { ...this.student } 
      }
    }
  }
  </script>
  