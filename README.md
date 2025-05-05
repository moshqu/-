<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حفظ القرآن الكريم</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #2c3e50;
            text-align: center;
        }
        .student-list {
            margin: 20px 0;
        }
        .student-card {
            background: #ecf0f1;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            cursor: pointer;
        }
        .progress-bar {
            height: 20px;
            background: #e0e0e0;
            border-radius: 10px;
            margin: 10px 0;
        }
        .progress {
            height: 100%;
            background: #27ae60;
            border-radius: 10px;
            width: 0%;
        }
        button {
            background: #3498db;
            color: white;
            border: none;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 10px 0;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: center;
        }
        th {
            background: #f2f2f2;
        }
        .completed {
            background: #2ecc71;
            color: white;
        }
        .missed {
            background: #e74c3c;
            color: white;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>برنامج متابعة حفظ القرآن الكريم</h1>
        
        <div id="add-student">
            <h2>إضافة طالب جديد</h2>
            <input type="text" id="student-name" placeholder="اسم الطالب">
            <button onclick="addStudent()">إضافة</button>
        </div>
        
        <div class="student-list" id="students">
            <h2>قائمة الطلاب</h2>
            <!-- الطلاب يظهرون هنا -->
        </div>
    </div>

    <script>
        let students = JSON.parse(localStorage.getItem('students')) || [];
        
        function renderStudents() {
            const studentsDiv = document.getElementById('students');
            studentsDiv.innerHTML = '<h2>قائمة الطلاب</h2>';
            
            students.forEach((student, index) => {
                const studentCard = document.createElement('div');
                studentCard.className = 'student-card';
                studentCard.innerHTML = `
                    <h3>${student.name}</h3>
                    <div class="progress-bar">
                        <div class="progress" style="width: ${student.progress}%"></div>
                    </div>
                    <button onclick="viewStudent(${index})">عرض التفاصيل</button>
                `;
                studentsDiv.appendChild(studentCard);
            });
        }
        
        function addStudent() {
            const name = document.getElementById('student-name').value;
            if (name.trim() === '') return;
            
            students.push({
                name: name,
                progress: 0,
                days: []
            });
            
            localStorage.setItem('students', JSON.stringify(students));
            document.getElementById('student-name').value = '';
            renderStudents();
        }
        
        function viewStudent(index) {
            const student = students[index];
            const studentsDiv = document.getElementById('students');
            
            studentsDiv.innerHTML = `
                <h2>${student.name}</h2>
                <button onclick="renderStudents()">العودة للقائمة</button>
                <table>
                    <tr>
                        <th>اليوم</th>
                        <th>الصفحة/السورة</th>
                        <th>الحفظ</th>
                        <th>تحديد</th>
                    </tr>
                    ${student.days.map((day, i) => `
                        <tr>
                            <td>${day.date}</td>
                            <td>${day.page}</td>
                            <td class="${day.completed ? 'completed' : 'missed'}">
                                ${day.completed ? '✓' : '✗'}
                            </td>
                            <td>
                                <button onclick="toggleDay(${index}, ${i})">تغيير</button>
                            </td>
                        </tr>
                    `).join('')}
                </table>
                <button onclick="addNewDay(${index})">إضافة يوم جديد</button>
            `;
        }
        
        function toggleDay(studentIndex, dayIndex) {
            students[studentIndex].days[dayIndex].completed = 
                !students[studentIndex].days[dayIndex].completed;
            updateProgress(studentIndex);
            localStorage.setItem('students', JSON.stringify(students));
            viewStudent(studentIndex);
        }
        
        function addNewDay(studentIndex) {
            const date = new Date().toLocaleDateString('ar-EG');
            const page = `صفحة ${students[studentIndex].days.length + 1}`;
            
            students[studentIndex].days.push({
                date: date,
                page: page,
                completed: false
            });
            
            updateProgress(studentIndex);
            localStorage.setItem('students', JSON.stringify(students));
            viewStudent(studentIndex);
        }
        
        function updateProgress(studentIndex) {
            const student = students[studentIndex];
            const totalDays = student.days.length;
            const completedDays = student.days.filter(day => day.completed).length;
            student.progress = totalDays === 0 ? 0 : Math.round((completedDays / totalDays) * 100);
            localStorage.setItem('students', JSON.stringify(students));
        }
        
        // التحميل الأولي
        renderStudents();
    </script>
</body>
</html>
