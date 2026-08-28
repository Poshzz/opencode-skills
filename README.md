# OpenCode & Antigravity Skills Collection

คอลเลกชัน **Production-Grade Agent Skills** สำหรับ **Antigravity (AGY)** และ **OpenCode** รวมพลังระหว่างชุดทักษะวิศวกรรมซอฟต์แวร์ระดับโลกจาก **Matt Pocock (AI Hero)** และชุดคัสตอมสกิลเฉพาะทางสำหรับ Review, Autonomous Loops, Debugging, Architecture และ Security

---

## 📦 รายชื่อ Skills ทั้งหมด (34 Skills)

### 1. 🚀 Main Engineering & Planning Flow (ขั้นตอนการพัฒนาตั้งแต่เริ่มจนส่งมอบ)
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **setup-matt-pocock-skills** ⚙️ | ตั้งค่า Repo ครั้งแรกให้ AI รู้จัก Issue Tracker, Triage Labels, Domain Docs | /setup-matt-pocock-skills, setup repo |
| **sk-matt** 🧭 | Interactive Router แนะนำ Skill หรือ Workflow ที่เหมาะกับสถานการณ์ปัจจุบัน | /ask-matt, ช่วยเลือก skill หน่อย |
| **grill-with-docs** 📋 | สัมภาษณ์/เค้นความต้องการ พร้อมอัปเดต Domain Model, CONTEXT.md และ ADRs | /grill-with-docs, grill with docs |
| **	o-spec** 📄 | สรุปบทสนทนาและไอเดียเป็นเอกสาร Specification มาตรฐาน | /to-spec, ทำ spec, เขียน spec |
| **	o-tickets** 🎫 | แตก Spec เป็น Tracer-Bullet Tickets ย่อยๆ พร้อมระบุ Blocking Dependencies | /to-tickets, แตกงาน, วางแผน tickets |
| **implement** 🔨 | ลงมือพัฒนาโค้ดตาม Spec/Tickets ขับเคลื่อนด้วย TDD และ Code Review | /implement, implement spec, เริ่มทำ ticket |
| **wayfinder** 🗺️ | วางแผนงานสเกลใหญ่ที่ต้องแบ่งทำหลาย Session ด้วย Decision Tickets | /wayfinder, วางแผนโปรเจกต์ใหญ่ |
| **	riage** 🏷️ | จัดการและเลื่อนสถานะ Issues/Tickets ผ่าน State Machine | /triage, คัดแยก issue |

---

### 2. 🔄 Review, Quality & Autonomous Convergence (ตรวจสอบและแก้อัตโนมัติ)
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **scrutinize-loop** 🔄 | **Autonomous Review & Repair Loop**<br>วนลูป Scrutinize ตรวจสอบโค้ด แก้ไขทันที และวนตรวจซ้ำจนกว่าจะไม่เจอบัค (VERDICT: SHIP) | /scrutinize-loop, /autofix-loop, วน scrutinize จนกว่าจะไม่เจอบัค |
| **scrutinize** 🔍 | Outsider-perspective End-to-End Review เจาะลึก Intent → Trace → Verify | /scrutinize, review code, audit diff |
| **rchitecture-review** 🏛️ | ตรวจสอบสถาปัตยกรรมระบบ โครงสร้าง Coupling, Cohesion, Scalability | /architecture-review, ตรวจสอบสถาปัตยกรรม |
| **security-review** 🔒 | Pre-release Security Audit ตรวจช่องโหว่ความปลอดภัยก่อน Deploy | /security-review, ตรวจความปลอดภัย |
| **performance-review** ⚡ | Data-driven Performance Engineering วิเคราะห์ Latency, P95/P99, CPU/RAM | /performance-review, ระบบช้า, optimize |
| **code-review** 🧐 | Two-Axis Review วิเคราะห์โค้ด 2 มิติพร้อมกัน (Coding Standards + Spec Match) | /code-review, review diff, ตรวจโค้ด |

---

### 3. 🛠️ Debugging, Testing & RCA (แก้บัค ทดสอบ และสรุปผล)
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **debug-mantra** 🛠️ | วินัย Debug 4 ขั้นตอน: Reproduce → Trace fail path → Falsify → Cross-reference | /debug-mantra, debug นี้หน่อย, แก้อาการนี้ |
| **diagnosing-bugs** 🩺 | Diagnosis Loop สำหรับบัคยาก: Red test → Minimise → Hypothesise → Fix | /diagnosing-bugs, diagnose bug, หาต้นตอบัค |
| **	dd** 🧪 | Test-Driven Development (Red-Green-Refactor) สร้างเทสต์คุณภาพสูงที่ทนต่อการ Refactor | /tdd, เขียนเทสก่อน, ทำ tdd |
| **post-mortem** 📝 | จัดทำบันทึกสรุปสาเหตุและวิธีการแก้ปัญหา (Root Cause Analysis - RCA) | /post-mortem, เขียน RCA, สรุปบัค |
| **prototype** 💡 | สร้าง Prototype โยนทิ้งเพื่อพิสูจน์ State Logic หรือทดลอง UI หลากหลายรูปแบบ | /prototype, ทำ prototype, ลองทำ ui mockup |

---

### 4. 🏛️ Architecture & Codebase Design (การออกแบบระบบและโมดูล)
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **improve-codebase-architecture** 📊 | สแกน Codebase หาจุด Deepening แล้วสรุปเป็น Visual HTML Report | /improve-codebase-architecture, สำรวจโครงสร้าง |
| **codebase-design** 📐 | ออกแบบ Deep Modules: ฟังก์ชันการทำงานสูงผ่าน Interface ที่เรียบง่าย | Auto-invoked / /codebase-design |
| **domain-modeling** 🏷️ | สร้างและขัดเกลา Domain Model, Glossary, CONTEXT.md และ ADRs | Auto-invoked / /domain-modeling |
| **karpathy-guidelines** 🧠 | Behavioral Guidelines ป้องกัน Common LLM Coding Mistakes & Overcomplication | Auto-invoked / Proactive coding |

---

### 5. 💬 Productivity & Communication (การเค้นไอเดียและการสื่อสาร)
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **grill-me** 🔥 | สัมภาษณ์/ซักไซ้ผู้ใช้อย่างเข้มข้นจนกว่าการตัดสินใจทุกกิ่งจะตกผลึก | /grill-me, สัมภาษณ์ฉันหน่อย, grill me |
| **grilling** 🎯 | Core Primitive สำหรับการสัมภาษณ์เจาะลึก (เบื้องหลัง grill-me และ grill-with-docs) | Auto-invoked |
| **handoff** 🤝 | สรุปบริบทและสถานะงานปัจจุบันเป็น Handoff Document เพื่อส่งต่อให้ Agent session ถัดไป | /handoff, สรุปส่งต่องาน |
| **	each** 🎓 | ให้ AI ทำหน้าที่เป็นครูผู้สอนเทคโนโลยี/คอนเซปต์ใหม่แบบ Interactive Workspace | /teach, สอนเรื่องนี้หน่อย |
| **	o-questionnaire** ❓ | แปลงการตัดสินใจที่ติดขัดเป็นแบบสอบถามสำหรับส่งต่อให้คนอื่นช่วยตอบ | /to-questionnaire, ทำแบบสอบถาม |
| **wait-what** 🤔 | ให้ AI อธิบายประเด็นที่เพิ่งพูดใหม่อย่างเรียบง่าย ชัดเจน ตรงไปตรงมา | /wait-what, ไม่เข้าใจ อธิบายใหม่ |
| **writing-for-agents** ✍️ | เทคนิคการเขียน Docs / Prompts / Rules ให้ Agent อ่านแล้วทำงานได้แม่นยำสูงสุด | Auto-invoked / /writing-for-agents |

---

### 6. 🧰 Utilities & Git Tools (เครื่องมือและตัวช่วยจัดการระบบ)
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **esolving-merge-conflicts** 🔀 | ช่วยเคลียร์ Git Conflict ทีละ Chunk โดยวิเคราะห์เจตนาดั้งเดิมของแต่ละฝั่ง | /resolving-merge-conflicts, แก้ merge conflict |
| **wizard** 🧙‍♂️ | สร้าง Interactive Bash Wizard นำทางมนุษย์ในขั้นตอน Setup, Provisioning หรือ Secret Config | /wizard, สร้าง wizard setup |
| **esearch** 📚 | ให้ Background Agent ไปสืบค้นข้อมูลเชิงลึกจาก Primary Source พร้อมอ้างอิง | /research, ค้นคว้าเรื่องนี้ |

---

### 7. 🎮 Specialized Skills
| Skill | หน้าที่หลัก | การใช้งาน / Trigger |
|---|---|---|
| **oblox-dev** 🕹️ | พัฒนาเกม Roblox ด้วย Luau, Client-Server RemoteEvents, DataStores, Anti-exploit | /roblox-dev, ทำเกม Roblox, เขียน Luau |

---

## 🚀 การติดตั้งและใช้งาน

### 1. ใช้งานใน Antigravity (AGY)
วางโฟลเดอร์สกิลทั้งหมดลงใน:
`ash
~/.gemini/config/skills/
`
*(ระบบ Antigravity จะตรวจจับและโหลด Skills เข้าสู่ระบบโดยอัตโนมัติ)*

### 2. ใช้งานใน OpenCode
วางโฟลเดอร์สกิลทั้งหมดลงใน:
`ash
~/.config/opencode/skills/
`

### 3. ใช้งานใน Claude Code / Agent อื่นๆ
วางโฟลเดอร์สกิลลงใน:
`ash
~/.agents/skills/
# หรือ
.claude/skills/
`
