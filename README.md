<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Open Exam Platform - คลังข้อสอบฟรี</title>
    <style>
        :root {
            --primary-color: #2563eb;
            --bg-color: #f3f4f6;
            --card-bg: #ffffff;
            --text-main: #1f2937;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            margin: 0;
            padding: 0;
        }
        header {
            background-color: var(--primary-color);
            color: white;
            padding: 1rem 2rem;
            text-align: center;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        .container {
            max-width: 800px;
            margin: 2rem auto;
            padding: 0 1rem;
        }
        .card {
            background-color: var(--card-bg);
            padding: 1.5rem;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            margin-bottom: 1.5rem;
        }
        h2 { margin-top: 0; border-bottom: 2px solid var(--bg-color); padding-bottom: 0.5rem; }
        
        /* Form Styles */
        .form-group { margin-bottom: 1rem; }
        label { display: block; margin-bottom: 0.5rem; font-weight: bold; }
        input, select {
            width: 100%;
            padding: 0.5rem;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 0.75rem 1.5rem;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: bold;
            width: 100%;
        }
        button:hover { background-color: #1d4ed8; }

        /* Exam List Styles */
        .exam-item {
            border-left: 4px solid var(--primary-color);
            background-color: var(--bg-color);
            padding: 1rem;
            margin-bottom: 1rem;
            border-radius: 4px;
        }
        .exam-item a {
            color: var(--primary-color);
            text-decoration: none;
            font-weight: bold;
        }
        .badge {
            background-color: #10b981;
            color: white;
            padding: 0.2rem 0.5rem;
            border-radius: 4px;
            font-size: 0.8rem;
            margin-left: 0.5rem;
        }
    </style>
</head>
<body>

    <header>
        <h1>📚 Open Exam Platform</h1>
        <p>คลังข้อสอบฟรี เพื่อความเท่าเทียมทางการศึกษา</p>
    </header>

    <div class="container">
        <div class="card">
            <h2>📤 อัปโหลดแนวข้อสอบ (สำหรับผู้สอน)</h2>
            <div class="form-group">
                <label for="school">โรงเรียนเป้าหมาย</label>
                <input type="text" id="school" placeholder="เช่น เตรียมอุดมศึกษา, มหิดลวิทยานุสรณ์">
            </div>
            <div class="form-group">
                <label for="subject">วิชา</label>
                <select id="subject">
                    <option value="คณิตศาสตร์">คณิตศาสตร์</option>
                    <option value="วิทยาศาสตร์">วิทยาศาสตร์</option>
                    <option value="ภาษาอังกฤษ">ภาษาอังกฤษ</option>
                    <option value="ภาษาไทย">ภาษาไทย</option>
                    <option value="สังคมศึกษา">สังคมศึกษา</option>
                </select>
            </div>
            <div class="form-group">
                <label for="year">ปี พ.ศ. (ที่สอบ)</label>
                <input type="number" id="year" placeholder="เช่น 2567">
            </div>
            <div class="form-group">
                <label for="url">ลิงก์ไฟล์ข้อสอบ (Google Drive / PDF)</label>
                <input type="url" id="url" placeholder="https://...">
            </div>
            <button onclick="uploadExam()">อัปโหลดข้อสอบ</button>
        </div>

        <div class="card">
            <h2>📖 คลังข้อสอบที่เปิดให้ใช้งาน</h2>
            <div id="exam-list">
                </div>
        </div>
    </div>

    <script>
        // จำลองฐานข้อมูลข้อสอบที่มีอยู่ในระบบ (จำลองฝั่ง Backend)
        let examDatabase = [
            { id: 1, school: "เตรียมอุดมศึกษา", subject: "คณิตศาสตร์", year: 2566, url: "#", uploader: "TutorA", isApproved: true },
            { id: 2, school: "มหิดลวิทยานุสรณ์", subject: "วิทยาศาสตร์", year: 2565, url: "#", uploader: "SchoolStaff", isApproved: true }
        ];

        // ฟังก์ชันแสดงรายการข้อสอบ
        function renderExams() {
            const examListDiv = document.getElementById('exam-list');
            examListDiv.innerHTML = ''; // ล้างค่าเดิมก่อน

            // กรองมาเฉพาะข้อสอบที่ผ่านการอนุมัติ (isApproved = true)
            const approvedExams = examDatabase.filter(exam => exam.isApproved);

            if (approvedExams.length === 0) {
                examListDiv.innerHTML = '<p>ยังไม่มีข้อสอบในระบบ</p>';
                return;
            }

            // วนลูปสร้าง HTML สำหรับข้อสอบแต่ละชุด
            approvedExams.forEach(exam => {
                const examDiv = document.createElement('div');
                examDiv.className = 'exam-item';
                examDiv.innerHTML = `
                    <h3>${exam.school} - ${exam.subject} (ปี ${exam.year}) <span class="badge">Verified</span></h3>
                    <p>แบ่งปันโดย: <strong>${exam.uploader}</strong></p>
                    <a href="${exam.url}" target="_blank">🔗 ดาวน์โหลด / ดูข้อสอบ</a>
                `;
                examListDiv.appendChild(examDiv);
            });
        }

        // ฟังก์ชันเมื่อกดปุ่มอัปโหลด
        function uploadExam() {
            const school = document.getElementById('school').value;
            const subject = document.getElementById('subject').value;
            const year = document.getElementById('year').value;
            const url = document.getElementById('url').value;

            // ตรวจสอบว่ากรอกข้อมูลครบไหม
            if (!school || !year || !url) {
                alert("กรุณากรอกข้อมูลให้ครบถ้วนครับ!");
                return;
            }

            // เพิ่มข้อมูลใหม่ลงใน Array (จำลองการบันทึกลง Database)
            // หมายเหตุ: ในเว็บจริง ค่า isApproved จะเป็น false เพื่อรอ Admin ตรวจสอบ
            // แต่นี่เป็นตัวอย่างให้เห็นภาพ เลยให้เป็น true เพื่อให้แสดงผลทันที
            const newExam = {
                id: examDatabase.length + 1,
                school: school,
                subject: subject,
                year: parseInt(year),
                url: url,
                uploader: "ผู้ใช้ทั่วไป (คุณ)", 
                isApproved: true 
            };

            examDatabase.push(newExam);
            
            // ล้างค่าในฟอร์ม
            document.getElementById('school').value = '';
            document.getElementById('year').value = '';
            document.getElementById('url').value = '';

            alert("อัปโหลดข้อสอบสำเร็จ! (จำลอง)");
            
            // อัปเดตหน้าจอ
            renderExams();
        }

        // เรียกใช้ฟังก์ชันแสดงข้อสอบเมื่อโหลดหน้าเว็บครั้งแรก
        window.onload = renderExams;
    </script>

</body>
</html>
