# Rules for Codex in This Workspace

Last updated: 2026-03-18

This file defines the operating rules for Codex in this workspace.
ไฟล์นี้กำหนดกติกาการทำงานของ Codex ใน workspace นี้

## Language Policy / นโยบายภาษา

- English rules are the authoritative wording for precision.
- คำอธิบายภาษาไทยมีไว้เพื่อให้ผู้ใช้เข้าใจง่ายขึ้น
- If the English and Thai wording ever seem to conflict, follow the stricter interpretation and ask the user before acting.
- ถ้าความหมายดูขัดกัน ให้ยึดแบบที่เข้มกว่าและถามผู้ใช้ก่อนลงมือ

## Scope / ขอบเขต

- Default working directory: `D:\Unreal Projects\FPSProject`
- พื้นที่ทำงานหลักคือ `D:\Unreal Projects\FPSProject`
- Stay inside this workspace by default.
- ให้ทำงานภายใน workspace นี้เป็นค่าเริ่มต้น
- Any access outside this workspace requires explicit user approval first.
- การแตะไฟล์หรือโฟลเดอร์นอก workspace นี้ต้องได้รับอนุญาตจากผู้ใช้ก่อนเสมอ

## Core Operating Principles / หลักการทำงานหลัก

- Inspect first, explain second, modify third.
- เริ่มจากอ่านและทำความเข้าใจก่อน แล้วค่อยอธิบาย จากนั้นจึงแก้ไขเมื่อได้รับคำสั่ง
- Prefer the smallest safe and reversible change.
- ให้เลือกการเปลี่ยนแปลงที่เล็กที่สุด ปลอดภัยที่สุด และย้อนกลับได้ง่ายที่สุด
- Never assume the user wants deletion, overwrite, reset, or cleanup unless they clearly said so.
- ห้ามเดาเองว่าผู้ใช้ต้องการลบ เขียนทับ รีเซ็ต หรือ cleanup ถ้ายังไม่ได้สั่งชัดเจน
- Before editing, clearly state what will be changed and where.
- ก่อนแก้ไข ต้องบอกให้ชัดว่าจะเปลี่ยนอะไรและที่ path ไหน
- If an action is ambiguous, risky, broad, or hard to undo, stop and ask first.
- ถ้าคำสั่งกำกวม เสี่ยง กว้างเกินไป หรือย้อนกลับยาก ให้หยุดและถามก่อน

## Absolute Prohibitions / สิ่งที่ห้ามทำเด็ดขาด

- Do not delete or wipe `D:\`, any whole drive, or any broad top-level directory.
- ห้ามลบหรือล้างทั้งไดรฟ์ `D:\` ไดรฟ์อื่น หรือโฟลเดอร์ระดับบนแบบกว้าง ๆ
- Do not delete or wipe `D:\Unreal Projects` or any sibling projects.
- ห้ามลบ `D:\Unreal Projects` หรือโปรเจกต์อื่นข้างเคียง
- Do not delete user folders such as `Desktop`, `Documents`, `Downloads`, `Pictures`, `Videos`, or `Saved Games`.
- ห้ามลบโฟลเดอร์ผู้ใช้ เช่น `Desktop`, `Documents`, `Downloads`, `Pictures`, `Videos`, `Saved Games`
- Do not run destructive filesystem commands on broad paths.
- ห้ามรันคำสั่งทำลายข้อมูลกับ path กว้าง ๆ
- Do not run destructive Git commands that discard work without explicit approval.
- ห้ามรันคำสั่ง Git ที่ทิ้งงานหรือรีเซ็ตงานโดยไม่ได้รับอนุญาตชัดเจน
- Do not format disks, edit partitions, or use boot/system recovery tools.
- ห้าม format ดิสก์ แก้พาร์ทิชัน หรือใช้เครื่องมือกู้คืน/บูตของระบบ
- Do not edit Windows Registry, Services, Startup, Firewall, Defender, drivers, or system policy.
- ห้ามแก้ Registry, Services, Startup, Firewall, Defender, drivers, หรือ system policy
- Do not install or uninstall system-wide software, plugins, drivers, or packages without approval.
- ห้ามติดตั้งหรือถอนซอฟต์แวร์ทั้งระบบ ปลั๊กอิน ไดรเวอร์ หรือแพ็กเกจโดยไม่ได้รับอนุญาต
- Do not download files from the internet or run installers without approval.
- ห้ามดาวน์โหลดไฟล์จากอินเทอร์เน็ตหรือรัน installer โดยไม่ได้รับอนุญาต
- Do not upload, exfiltrate, or share project files, secrets, logs, or personal data without approval.
- ห้ามอัปโหลด ส่งออก หรือแชร์ไฟล์โปรเจกต์ ความลับ log หรือข้อมูลส่วนตัวโดยไม่ได้รับอนุญาต
- Do not read or reveal credentials unless the user explicitly asks for that exact file or value.
- ห้ามอ่านหรือเปิดเผย credentials ถ้าผู้ใช้ไม่ได้ขออย่างเจาะจงจริง ๆ
- Do not shut down, restart, log off, or kill important processes without approval.
- ห้ามปิดเครื่อง รีสตาร์ต log off หรือ kill process สำคัญโดยไม่ได้รับอนุญาต

## Sensitive Targets / จุดที่ถือว่าอ่อนไหว

- `D:\`
- `D:\Unreal Projects`
- Anything outside `D:\Unreal Projects\FPSProject`
- `.git`
- `.env`, `.pem`, `.key`, `.p12`, `.mobileprovision`
- `.uproject`
- `Config`
- `Saved`
- Binary Unreal assets such as `.uasset` and `.umap`

## Actions Requiring Explicit Approval / สิ่งที่ต้องขออนุญาตก่อน

- Any operation outside `D:\Unreal Projects\FPSProject`
- Any delete, move, rename, or overwrite operation
- Any bulk edit across many files
- Any command that downloads dependencies, packages, binaries, or plugins
- Any command that needs elevated permissions
- Any edit to `Config`, `Saved`, `.uproject`, `.uasset`, or `.umap`
- Any Git command that can discard local work
- Any action that may interrupt the Unreal Editor, stop services, or close active programs

## Approval Standard / มาตรฐานการขออนุญาต

- Approval must be explicit, not implied.
- การอนุญาตต้องชัดเจน ไม่ใช่การเดาเอา
- Before asking for approval, state the exact target path, exact action, and expected impact.
- ก่อนขออนุญาต ต้องระบุ path ที่จะโดน คำสั่งที่จะทำ และผลกระทบที่คาดไว้
- If the user says "yes", apply that approval only to the specific action described.
- ถ้าผู้ใช้ตอบว่า "ได้" ให้ถือว่าอนุญาตเฉพาะงานที่อธิบายไปเท่านั้น
- Do not expand a narrow approval into a broader destructive action.
- ห้ามเอาการอนุญาตแคบ ๆ ไปตีความเป็นสิทธิ์ทำเรื่องเสี่ยงที่กว้างกว่าเดิม

## Commands to Avoid / ตัวอย่างคำสั่งที่ควรหลีกเลี่ยง

```powershell
Remove-Item D:\ -Recurse -Force
Remove-Item "D:\Unreal Projects" -Recurse -Force
Remove-Item "$env:USERPROFILE\Documents" -Recurse -Force
del /f /s /q D:\*
rd /s /q "D:\Unreal Projects"
git reset --hard
git clean -fd
git clean -fdx
git checkout -- .
git restore .
diskpart
format D:
bcdedit
shutdown /s /t 0
Restart-Computer -Force
taskkill /f /im explorer.exe
```

## Safer Defaults / ค่าเริ่มต้นที่ปลอดภัยกว่า

- Prefer read-only inspection when the user is asking exploratory questions.
- ถ้าผู้ใช้กำลังถามเชิงสำรวจ ให้เน้นอ่านอย่างเดียวก่อน
- Prefer adding new files over overwriting important existing files when possible.
- ถ้าเป็นไปได้ ให้เพิ่มไฟล์ใหม่แทนการเขียนทับไฟล์สำคัญเดิม
- Prefer patch-sized edits over broad refactors unless the user asked for a broader change.
- ให้แก้แบบเป็นจุดเล็ก ๆ แทนการรีแฟกเตอร์ใหญ่ ถ้าผู้ใช้ไม่ได้ขอให้เปลี่ยนกว้าง
- Prefer non-destructive Git inspection commands over cleanup/reset commands.
- ให้ใช้คำสั่ง Git แบบตรวจสอบข้อมูล แทนคำสั่ง cleanup หรือ reset ที่ทำลายงาน

## Allowed by Default / สิ่งที่ทำได้เป็นค่าเริ่มต้น

- Read files inside the workspace
- Explain code, project structure, logs, and errors
- Create new files or folders inside the workspace when the user asks
- Make targeted code edits inside the workspace when the user asks
- Run project-local, non-destructive commands for inspection or verification

## Conflict Resolution / วิธีตัดสินเมื่อกฎชนกัน

- If any instruction conflicts with this file, follow the safer interpretation and ask the user to confirm.
- ถ้าคำสั่งใดขัดกับไฟล์นี้ ให้ยึดแบบที่ปลอดภัยกว่าและขอคำยืนยันอีกครั้ง
- If a future request is catastrophic in scope, refuse the broad form and ask for a narrower, safer action.
- ถ้าคำขอในอนาคตกว้างหรืออันตรายเกินไป ให้ปฏิเสธรูปแบบกว้างนั้นและขอให้ผู้ใช้ระบุงานที่แคบและปลอดภัยกว่า

## Practical Intent / เจตนาเชิงปฏิบัติ

- This file exists to protect the user's files, system, project history, and ongoing work.
- ไฟล์นี้มีไว้เพื่อปกป้องไฟล์ของผู้ใช้ ระบบ โปรเจกต์ และงานที่กำลังทำอยู่
- When in doubt, be conservative.
- ถ้าไม่แน่ใจ ให้เลือกทางที่ระมัดระวังไว้ก่อน
