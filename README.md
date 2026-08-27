# OpenCode & Antigravity Skills Collection

คอลเลกชัน Custom Skills สำหรับ **Antigravity (AGY)** และ **OpenCode** เพื่อยกระดับความสามารถในการ Review, Debug, Refactor, วางแผน, และทดสอบระบบอย่างเป็นมืออาชีพ

---

## 📦 รายชื่อ Skills ทั้งหมด (10 Skills)

| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **`scrutinize-loop`** 🔄 | **Autonomous Review & Repair Loop**<br>วนลูป Scrutinize หาข้อบกพร่อง แก้ไขทันที และวนตรวจซ้ำจนกว่าจะไม่เจอบัค (VERDICT: SHIP) | `/scrutinize-loop`, `/autofix-loop`, "วน scrutinize จนกว่าจะไม่เจอบัค" |
| **`scrutinize`** 🔍 | Outsider-perspective End-to-End Review สำหรับ Code, PR, หรือ Plan | `/scrutinize`, "review code", "audit diff" |
| **`debug-mantra`** 🛠️ | ระเบียบวิธี Debug 4 ขั้นตอน: Reproduce → Trace → Falsify → Cross-reference | `/debug-mantra`, "debug นี้หน่อย", "แก้อาการนี้" |
| **`architecture-review`** 🏛️ | ตรวจสอบสถาปัตยกรรมระบบ โครงสร้าง ความยืดหยุ่น และ Scalability | `/architecture-review`, "ตรวจสอบสถาปัตยกรรม" |
| **`security-review`** 🔒 | Pre-release Security Audit ตรวจสอบช่องโหว่ความปลอดภัยก่อน Deploy | `/security-review`, "ตรวจความปลอดภัย" |
| **`performance-review`** ⚡ | Data-driven Performance Engineering วิเคราะห์ Latency, P95/P99, CPU/RAM | `/performance-review`, "ระบบช้า", "optimize" |
| **`karpathy-guidelines`** 🧠 | Behavioral Guidelines ป้องกัน Common LLM Coding Mistakes | Proactive coding / refactoring |
| **`to-tickets`** 🎫 | Tracer-Bullet Planning แตกแผนงานใหญ่เป็น Actionable Linear Tickets | `/to-tickets`, "แตกงาน", "วางแผน" |
| **`post-mortem`** 📝 | จัดทำบันทึกสรุปสาเหตุและวิธีการแก้ปัญหา (Root Cause Analysis - RCA) | `/post-mortem`, "เขียน RCA", "สรุปบัค" |
| **`tdd`** 🧪 | Test-Driven Development (Red-Green-Refactor) | `/tdd`, "เขียนเทสก่อน", "ทำ tdd" |

---

## 🚀 การติดตั้งและใช้งาน

### 1. Antigravity Configuration
เพิ่ม Path ใน `~/.gemini/config/skills.json` หรือคัดลอกลง `~/.agents/skills/`

### 2. OpenCode Configuration
วางโฟลเดอร์ลงใน `~/.config/opencode/skills/`
