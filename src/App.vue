<template>
  
  <div id="app" class="font-prompt">
    
    

    
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark px-4 py-3 shadow-sm">
      <a class="navbar-brand fs-3 fw-bold" href="#">🐨 My Profile</a>
    </nav>

    <!-- Intro + Image -->
    <div class="container-fluid py-5">
      <div class="row justify-content-center align-items-center min-vh-75">
        <div class="col-md-5 text-center text-md-start">
          <h1 class="display-7 fw-bold text-success mb-4 text-nowrap">
  สวัสดีครับผม{{ profile.name ? ' ' + profile.name : '' }}
</h1>

          <p class="lead text-secondary">{{ profile.description }}</p>
          
        </div>
        <div class="col-md-6 text-center mt-5 mt-md-0">
          <img
            v-if="currentImage"
            :src="currentImage"
            class="profile-img img-fluid rounded-4 shadow-lg"
            alt="Profile Image"
          />
        </div>
      </div>
    </div>

    <!-- ข้อมูลนิสิต -->
    <div class="container my-5">
      <div class="card shadow">
        <div class="card-body">
          <h4 class="text-success fw-bold mb-4">ข้อมูลนิสิต</h4>

          <div v-if="!editingStudent">
            <p><strong>ชื่อ-นามสกุล:</strong> {{ student.name }}</p>
            <p><strong>รหัสนิสิต:</strong> {{ student.id }}</p>
            <p><strong>สาขา:</strong> {{ student.major }}</p>
            <p><strong>โรงเรียนเดิม:</strong> {{ student.school }}</p>
            <button class="btn btn-success" @click="editingStudent = true">แก้ไข</button>
          </div>

          <div v-else>
            <input class="form-control mb-2" v-model="editedStudent.name" placeholder="ชื่อ-นามสกุล" />
            <input class="form-control mb-2" v-model="editedStudent.id" placeholder="รหัสนิสิต" />
            <input class="form-control mb-2" v-model="editedStudent.major" placeholder="สาขา" />
            <input class="form-control mb-2" v-model="editedStudent.school" placeholder="โรงเรียนเดิม" />
            <button class="btn btn-primary me-2" @click="saveStudent">บันทึก</button>
            <button class="btn btn-secondary" @click="editingStudent = false">ยกเลิก</button>
          </div>
        </div>
      </div>
    </div>

    <!-- รายละเอียดการเรียน -->
    <div class="container my-5">
      <div class="card shadow">
        <div class="card-body">
          <h4 class="text-success fw-bold mb-4">รายละเอียดการเรียน</h4>

          <div v-for="(course, index) in courses" :key="index" class="mb-3 border-bottom pb-3">
            <div v-if="!course.editing">
              <p><strong>รหัสวิชา:</strong> {{ course.code }}</p>
              <p><strong>ชื่อวิชา:</strong> {{ course.name }}</p>
              <p><strong>หน่วยกิต:</strong> {{ course.credit }}</p>
              <p><strong>เกรด:</strong> {{ course.grade }}</p>
              <button class="btn btn-sm btn-warning me-2" @click="editCourse(index)">แก้ไข</button>
              <button class="btn btn-sm btn-danger" @click="deleteCourse(index)">ลบ</button>
            </div>
            <div v-else>
              <input class="form-control mb-2" v-model="course.code" placeholder="รหัสวิชา" />
              <input class="form-control mb-2" v-model="course.name" placeholder="ชื่อวิชา" />
              <input class="form-control mb-2" v-model="course.credit" placeholder="หน่วยกิต" />
              <input class="form-control mb-2" v-model="course.grade" placeholder="เกรด" />
              <button class="btn btn-sm btn-primary me-2" @click="saveCourse(index)">บันทึก</button>
              <button class="btn btn-sm btn-secondary" @click="cancelEditCourse(index)">ยกเลิก</button>
            </div>
          </div>

          <button class="btn btn-success mt-3" @click="addNewCourse">➕ เพิ่มรายวิชา</button>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <footer class="bg-dark text-white text-center py-4">
      © 2025 - 
    </footer>
  </div>
</template>

<script>
import axios from 'axios'
import StudentInfo from './components/StudentInfo.vue' // 
components: {
  StudentInfo
}

export default {
  name: 'App',
  components: {
    StudentInfo // 
  },
  data() {
    return {
      profile: {},
      imageList: [],
      currentImageIndex: 0,
      currentImage: null,

      student: {
        name: 'นายธนกฤต สิริศรีศักดิ์เดช',
        id: '6630250141',
        major: 'วิทยาการคอมพิวเตอร์',
        school: 'โรงเรียนสวนกุหลาบวิทยาลัย'
      },
      editedStudent: {},
      editingStudent: false,

      courses: [
        { code: 'CS101', name: 'Programming', credit: 3, grade: 'A', editing: false },
        { code: 'CS102', name: 'Data Structures', credit: 3, grade: 'B+', editing: false }
      ]
    }
  },
  mounted() {
  axios.get('http://localhost:3000/profile')
    .then(response => {
      this.profile = response.data
      this.imageList = [response.data.image, "/profile2.jpg"]
      this.currentImage = this.imageList[0]
      setInterval(this.switchImage, 3000)
    })
    .catch(error => {
      console.error('เกิดข้อผิดพลาดในการดึงข้อมูล:', error)
    })

  
  const savedStudent = localStorage.getItem('student')
  if (savedStudent) {
    this.student = JSON.parse(savedStudent)
  }

  
  const savedCourses = localStorage.getItem('courses')
  if (savedCourses) {
    this.courses = JSON.parse(savedCourses)
  }

  this.editedStudent = { ...this.student }
},

  methods: {
    switchImage() {
      this.currentImageIndex = (this.currentImageIndex + 1) % this.imageList.length
      this.currentImage = this.imageList[this.currentImageIndex]
    },
    saveStudent() {
  this.student = { ...this.editedStudent }
  this.editingStudent = false
  // 💾 Save to localStorage
  localStorage.setItem('student', JSON.stringify(this.student))
},

    editCourse(index) {
      this.courses[index].editing = true
    },
    saveCourse(index) {
  this.courses[index].editing = false
  delete this.courses[index].isNew 
  localStorage.setItem('courses', JSON.stringify(this.courses))
},



cancelEditCourse(index) {
  if (this.courses[index].isNew) {
    this.courses.splice(index, 1) 
  } else {
    this.courses[index].editing = false
  }
},


    deleteCourse(index) {
  this.courses.splice(index, 1)
  localStorage.setItem('courses', JSON.stringify(this.courses))
},

addNewCourse() {
  this.courses.push({
    code: '',
    name: '',
    credit: '',
    grade: '',
    editing: true,
    isNew: true 
  })
}



  }
}
</script>


<style>
@import url('https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;600&display=swap');

#app {
  background: linear-gradient(to bottom right, #e8f5e9, #ffffff);
  min-height: 100vh;
}

.font-prompt {
  font-family: 'Prompt', sans-serif;
}

.min-vh-75 {
  min-height: 75vh;
}

.profile-img {
  width: 50%;
  max-height: 450px;
  object-fit: cover;
  object-position: center;
}

</style>
