<template>
  <div class="container">
    <h2>Chỉnh Sửa Đăng Ký Học Phần</h2>
    <form @submit.prevent="updateRegistration">
      <!-- Form để chỉnh sửa thông tin -->
      <input v-model="editedRegistration.fullName" type="text" placeholder="Họ và Tên" required>
      <input v-model="editedRegistration.studentID" type="text" placeholder="MSSV" required>
      <select v-model="editedRegistration.semester" required>
        <option value="1">Học Kỳ 1</option>
        <option value="2">Học Kỳ 2</option>
      </select>
      <select v-model="editedRegistration.courseId" required>
        <option v-for="course in courses" :key="course.id" :value="course.id">{{ course.name }}</option>
      </select>
      <button type="submit">Cập Nhật</button>
    </form>
  </div>
</template>

<script>
import { bookRef, update } from '@/firebase';

export default {
  data() {
    return {
      editedRegistration: {
        id: '',
        fullName: '',
        studentID: '',
        semester: '',
        courseId: ''
      },
      courses: [] // Danh sách môn học từ Firebase
    };
  },
  methods: {
    fetchRegistration() {
      const registrationId = this.$route.params.id; // Lấy ID từ đường dẫn
      if (registrationId) {
        bookRef.child(registrationId).once('value', snapshot => {
          const registration = snapshot.val();
          if (registration) {
            this.editedRegistration = {
              id: registrationId,
              fullName: registration.fullName || '',
              studentID: registration.studentID || '',
              semester: registration.semester || '',
              courseId: registration.courseId || ''
            };
          } else {
            console.error(`Không tìm thấy đăng ký với ID: ${registrationId}`);
          }
        });
      } else {
        console.error('Không có ID được truyền vào đường dẫn');
      }
    },
    updateRegistration() {
      update(bookRef.child(this.editedRegistration.id), this.editedRegistration)
        .then(() => {
          console.log('Cập nhật thành công!');
          // Redirect hoặc hiển thị thông báo thành công
        })
        .catch(error => {
          console.error('Cập nhật thất bại:', error);
          // Hiển thị thông báo lỗi
        });
    }
  },
  created() {
    this.fetchRegistration(); // Lấy dữ liệu để chỉnh sửa khi component được tạo
  }
};
</script>

<style>
/* CSS styles here */
</style>
