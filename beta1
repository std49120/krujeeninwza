#include <iostream>
#include <vector>
#include <string>
#include <memory>

using namespace std;

// 1. กำหนดสิทธิ์ของผู้ใช้งาน (User Roles)
enum class Role { STUDENT, CONTRIBUTOR, ADMIN };

struct User {
    string username;
    Role role;
    bool isVerified = false; // สำหรับสถาบันหรือติวเตอร์ที่ต้องผ่านการตรวจสอบ
};

// 2. โครงสร้างข้อมูลข้อสอบ (Exam Structure)
struct Exam {
    string schoolName;
    int year;
    string subject;
    string contentUrl; // ลิงก์ดาวน์โหลดหรือเนื้อหาข้อสอบ
    string uploaderName;
    bool isApproved = false; // ระบบความปลอดภัย: ต้องได้รับการตรวจสอบก่อนแสดงผล
};

// 3. ระบบจัดการแพลตฟอร์มคลังข้อสอบ (Platform Management System)
class OpenExamPlatform {
private:
    vector<User> users;
    vector<Exam> examDatabase;

public:
    // ระบบสมัครสมาชิก
    void registerUser(string username, Role role) {
        User newUser{username, role, (role == Role::STUDENT)}; // นักเรียนไม่ต้องยืนยันตัวตน เข้าถึงได้ทันที
        users.push_back(newUser);
        cout << "[System] ลงทะเบียนผู้ใช้: " << username << " สำเร็จ!\n";
    }

    // ระบบยืนยันตัวตนผู้ตรวจสอบ/สถาบัน (Admin เท่านั้นที่ทำได้)
    void verifyContributor(string adminName, string contributorName) {
        cout << "\n--- [Admin Action] โดย " << adminName << " ---\n";
        for (auto& user : users) {
            if (user.username == contributorName && user.role == Role::CONTRIBUTOR) {
                user.isVerified = true;
                cout << "[Success] สถาบัน/ผู้สอน " << contributorName << " ได้รับการยืนยันตัวตนแล้ว (Verified)\n";
                return;
            }
        }
        cout << "[Error] ไม่พบรายชื่อผู้สร้างเนื้อหาที่ต้องการยืนยัน\n";
    }

    // ระบบเพิ่มข้อสอบ (Contributors)
    void uploadExam(const User& user, string school, int year, string subject, string url) {
        if (user.role != Role::CONTRIBUTOR) {
            cout << "[Denied] เฉพาะอาจารย์หรือสถาบันเท่านั้นที่เพิ่มข้อสอบได้\n";
            return;
        }

        Exam newExam{school, year, subject, url, user.username, false};
        
        // ความปลอดภัย: ถ้าเป็น Contributor ที่ยืนยันตัวตนแล้ว ข้อสอบจะอนุมัติอัตโนมัติ
        if (user.isVerified) {
            newExam.isApproved = true;
            cout << "[Upload] " << user.username << " อัปโหลดข้อสอบ " << school << " (" << subject << " ปี " << year << ") สำเร็จ (Auto-Approved)\n";
        } else {
            cout << "[Upload Pending] " << user.username << " อัปโหลดข้อสอบแล้ว กำลังรอ Admin ตรวจสอบความถูกต้อง...\n";
        }
        
        examDatabase.push_back(newExam);
    }

    // ระบบอนุมัติข้อสอบโดย Admin
    void approveExam(string adminName, int index) {
        if (index >= 0 && index < examDatabase.size()) {
            examDatabase[index].isApproved = true;
            cout << "[Admin] อนุมัติข้อสอบรหัส " << index << " เรียบร้อยแล้ว\n";
        }
    }

    // ระบบค้นหาและเข้าถึงข้อสอบ (ฟรีสำหรับทุกคน)
    void viewAvailableExams() const {
        cout << "\n=========================================\n";
        cout << "   คลังข้อสอบฟรีเพื่อความเท่าเทียมทางการศึกษา   \n";
        cout << "=========================================\n";
        
        bool hasExams = false;
        for (const auto& exam : examDatabase) {
            // ดึงเฉพาะข้อสอบที่ปลอดภัยและผ่านการอนุมัติแล้วเท่านั้น
            if (exam.isApproved) {
                cout << "-> โรงเรียน: " << exam.schoolName 
                     << " | วิชา: " << exam.subject 
                     << " | ปีสอบ: " << exam.year << "\n"
                     << "   [Link] " << exam.contentUrl 
                     << " (แบ่งปันโดย: " << exam.uploaderName << ")\n"
                     << "-----------------------------------------\n";
                hasExams = true;
            }
        }
        
        if (!hasExams) {
            cout << "ยังไม่มีข้อสอบที่เปิดให้ใช้งานในขณะนี้\n";
        }
    }
};

// 4. ทดสอบการทำงานของระบบ (Main Function)
int main() {
    OpenExamPlatform platform;

    // สร้าง User จำลอง
    User admin{"TriamUdom_Admin", Role::ADMIN, true};
    User tutorA{"Tutor_P'Ond", Role::CONTRIBUTOR, false}; // เริ่มต้นยังไม่ยืนยันตัวตน
    User student{"Nong_Somchai", Role::STUDENT, true};     // นักเรียนเข้าใช้งานได้เลย

    platform.registerUser(tutorA.username, Role::CONTRIBUTOR);
    platform.registerUser(student.username, Role::STUDENT);

    // ลองอัปโหลดข้อสอบตอนที่ยังไม่ยืนยันตัวตน (จะติดสถานะ Pending เพื่อความปลอดภัย)
    platform.uploadExam(tutorA, "เตรียมอุดมศึกษา", 2025, "คณิตศาสตร์", "https://open-exam.org/triam-math-25");

    // นักเรียนเข้ามาดู... จะยังไม่เห็นข้อสอบที่ยังไม่ผ่านการตรวจสอบ
    platform.viewAvailableExams();

    // Admin ทำการตรวจสอบสถาบัน/ติวเตอร์ท่านนี้
    platform.verifyContributor(admin.username, tutorA.username);
    tutorA.isVerified = true; // อัปเดตสถานะจำลองในฝั่ง User

    // อนุมัติข้อสอบเก่าที่เคยค้างไว้
    platform.approveExam(admin.username, 0);

    // ติวเตอร์ที่ Verified แล้ว อัปโหลดข้อสอบชุดใหม่ (ระบบจะอนุมัติทันที)
    platform.uploadExam(tutorA, "มหิดลวิทยานุสรณ์", 2024, "วิทยาศาสตร์", "https://open-exam.org/mwit-sci-24");

    // นักเรียนเข้ามาดูอีกครั้ง ตอนนี้จะเข้าถึงข้อสอบทั้งหมดได้ฟรีและปลอดภัย
    platform.viewAvailableExams();

    return 0;
}
