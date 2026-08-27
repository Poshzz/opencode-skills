# OpenCode Skills Collection 🚀

คอลเลกชันรวม **Skills** และแนวทางปฏิบัติ (Guidelines) สำหรับใช้งานร่วมกับ **OpenCode**, **Antigravity**, **Claude Code**, และ AI Coding Assistants

---

## 📦 รายการ Skills ทั้งหมด (9 Skills)

| Skill | คำอธิบายสั้น | ตัวอย่าง Use Cases |
| :--- | :--- | :--- |
| [`architecture-review`](./architecture-review/SKILL.md) | วิเคราะห์และตรวจสอบสถาปัตยกรรมระบบ (Module design, Coupling/Cohesion, Scalability, Trade-offs) | ตรวจสอบโครงสร้างโปรเจกต์ก่อนเริ่มขยายระบบ, ประเมิน Tech Stack |
| [`debug-mantra`](./debug-mantra/SKILL.md) | ระเบียบวินัย 4 ขั้นตอนในการดีบัก (Reproducibility, Fail Path, Disprove Hypothesis, Breadcrumbs) | ใช้เมื่อเกิดบั๊กที่หายาก ต้องการขั้นตอนวิเคราะห์อย่างเป็นระบบ |
| [`karpathy-guidelines`](./karpathy-guidelines/SKILL.md) | กฎและแนวทางปฏิบัติตาม Andrej Karpathy เพื่อลดข้อผิดพลาดของ LLM (Think before coding, Surgical changes) | ใช้เป็น system prompt / instruction ในการเขียนโค้ดและ Refactor |
| [`performance-review`](./performance-review/SKILL.md) | วิเคราะห์ Performance แบบ Data-driven (p50/p95/p99 latency, throughput, memory peak, CPU, N+1 queries) | ตรวจจับคอขวด, วิเคราะห์ memory leak, ตรวจสอบ slow query ก่อน release |
| [`post-mortem`](./post-mortem/SKILL.md) | การเขียนรายงานบันทึกสรุปหลังแก้ไขบั๊ก (Root Cause Analysis - RCA, Fix, Prevention) | ใช้สร้างเอกสาร RCA ทางวิศวกรรมสำหรับทีมหลังจากแก้ปัญหาเสร็จ |
| [`scrutinize`](./scrutinize/SKILL.md) | ตรวจสอบ Plan, PR หรือโค้ดจากมุมมองภายนอก (Outsider perspective) และ End-to-end code path tracing | ขอ Second opinion ในการ Review PR หรือ Design Doc |
| [`security-review`](./security-review/SKILL.md) | ตรวจสอบช่องโหว่ความปลอดภัยก่อน Deploy (Auth, Injection, CORS, Data leaks, Misconfig) | ทำ Security audit ก่อนส่งมอบงานหรือปล่อยขึ้น Production |
| [`tdd`](./tdd/SKILL.md) | แนวทางการเขียนโค้ดแบบ Test-Driven Development (Red-Green-Refactor, Mocking rules, Test specs) | ออกแบบและเขียน Automated Unit/Integration Tests |
| [`to-tickets`](./to-tickets/SKILL.md) | แตก Plan / Spec ออกเป็น Tracer-bullet tickets พร้อมกำหนด Block dependencies | แปลง Feature requirements เป็น Task ย่อยที่พร้อมนำไปลงมือทำ |

---

## 🛠️ วิธีการติดตั้งและใช้งานใน OpenCode

### ติดตั้งลงในเครื่อง (Local)
คัดลอกโฟลเดอร์ Skills ไปไว้ที่ `~/.config/opencode/skills/`:

```bash
mkdir -p ~/.config/opencode/skills
cp -r */ ~/.config/opencode/skills/
```

### การตั้งค่าใน `opencode.jsonc`
สามารถอ้างอิง instruction เช่น `karpathy-guidelines.md` หรือ config ได้ในไฟล์คอนฟิกของ OpenCode:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "~/.config/opencode/skills/karpathy-guidelines/SKILL.md"
  ]
}
```

---

## 📄 โครงสร้างไดเรกทอรี

```
.
├── architecture-review/
│   └── SKILL.md
├── debug-mantra/
│   └── SKILL.md
├── karpathy-guidelines/
│   └── SKILL.md
├── performance-review/
│   ├── SKILL.md
│   └── tests/
│       └── validation-scenarios.md
├── post-mortem/
│   └── SKILL.md
├── scrutinize/
│   └── SKILL.md
├── security-review/
│   └── SKILL.md
├── tdd/
│   ├── SKILL.md
│   ├── mocking.md
│   ├── tests.md
│   └── agents/
│       └── openai.yaml
├── to-tickets/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
└── README.md
```
