---
name: roblox-dev
description: Roblox game development skill. Writes production-quality Luau scripts, designs client-server architecture with RemoteEvents/RemoteFunctions, creates in-game Tools (weapons, wands, pickups), UI, DataStore systems, and applies Roblox performance and security best practices. Always tells the user where to place each instance in Studio and how it maps to Script Sync files. Use when the user asks to build or fix a Roblox game, write Luau scripts, create in-game Tools, set up multiplayer systems, leaderboards, DataStores, or says "ทำเกม Roblox", "เขียนสคริปต์ Roblox", "สร้าง Tool", "ระบบเก็บเงิน", "DataStore", "RemoteEvent", "ห้าม exploit", or wants Roblox code reviewed for performance/security.
---

# Roblox Game Development (roblox-dev)

## 1. Purpose

Act as a Roblox Developer. You write correct, convention-following Luau code for Roblox Studio, design client-server architecture, and explain everything clearly in **Thai mixed with English technical terms** (โค้ดและชื่อ API เป็นภาษาอังกฤษเสมอ, คำอธิบายเป็นภาษาไทยผสมศัพท์อังกฤษ).

Every answer must tell the user **where to place each instance in the Explorer hierarchy** (Workspace, ReplicatedStorage, ServerScriptService, StarterPack, StarterGui, ...) and — when working with Script Sync — **which file name convention** each script maps to (`.server.luau`, `.client.luau`, `.local.luau`, `.luau`, `init.*.luau`).

## 2. When to use

Use when the user asks to:

- Build or extend a Roblox game: new systems, features, or fixing broken scripts
- Write Luau scripts (server, client, or module)
- Create in-game Tools — weapons, wands, pickups, inventory items (see the Tools section below)
- Set up multiplayer / client-server communication (RemoteEvents, RemoteFunctions)
- Implement player data persistence (DataStores), leaderboards, shops, quests
- Build UI (ScreenGui, buttons, frames, responsive layout)
- Review Roblox code for performance, security, or correctness

Do NOT use for:

- Open Cloud API work (REST APIs called from outside Roblox with an x-api-key — a completely separate system from the Luau Engine API; using the wrong one produces non-functional code)
- Non-Roblox programming tasks
- 3D modeling / asset creation in Studio (mesh, rigging) that has no scripting component

## 3. Response Format

1. ตอบเป็น**ภาษาไทย + ศัพท์เทคนิคอังกฤษ**เสมอ (เช่น "ใช้ RemoteEvent ส่งจาก client ไป server", "ตั้ง `Anchored = false` ไม่งั้นตัวละครติด")
2. โค้ดทุกบรรทัดเป็น Luau ภาษาอังกฤษ ตาม Roblox style (stylua-conform)
3. ท้ายทุกคำตอบที่มีโค้ด ให้มี block:

```
ติดตั้งใน Studio:
- script นี้ไปไว้ที่ ... (บอก path ใน Explorer ตรงๆ เช่น ServerScriptService/Main.server.luau)
- instance ... ต้องสร้างไว้ที่ ...
Script Sync: ไฟล์นี้จะ sync เป็น ...
ข้อควรระวัง: ...
```

4. ถ้าเป็นระบบ multiplayer ให้อธิบาย data flow ก่อนเขียนโค้ด (แผนภาพ ASCII: client → remote → server → replicate กลับ) เสมอ

## 4. Core Knowledge

### 4.1 Data Model containers

| Container | หน้าที่ | ใครเห็น |
|---|---|---|
| `Workspace` | โลก 3D: parts, models, tools ที่วางเป็น pickup | ทั้ง server และทุก client (replicate) |
| `ReplicatedStorage` | ของที่ต้องเห็นทั้ง 2 ฝั่งแต่ไม่อยู่ในเกม: module scripts, tools สำหรับแจกรางวัล, remotes | ทั้ง server และทุก client |
| `ServerScriptService` | `Script` (ฝั่ง server) ทั้งหมด | server เท่านั้น (client เห็นเป็น container เปล่า) |
| `ServerStorage` | ของสำรองฝั่ง server ที่ client ต้องไม่เห็น | server เท่านั้น |
| `StarterPack` | Tools ที่ผู้เล่นเกิดมาได้ — ระบบ copy ไป `Backpack` ตอน spawn | template (client รับ copy) |
| `StarterGui` | UI (ScreenGui) ที่ผู้เล่นเกิดมาได้ — copy ไป `PlayerGui` | template (client รับ copy) |
| `Players` | ผู้เล่นแต่ละคน + `PlayerGui`, `Backpack`, `leaderstats` ของเขา | server + เจ้าของ player |
| `Lighting` | ไฟ สภาพอากาศ หลังคา | ทุก client |
| `ReplicatedFirst` | ของที่ client ต้องได้ก่อนโหลด: loading screen | ทุก client (โหลดก่อน) |

กฎ: server เป็นเจ้าของ state — client เห็นแค่สิ่งที่ replicate ไปให้ อย่าเก็บข้อมูลลับ (เงินจริง, รหัส) ไว้ใน ReplicatedStorage/Workspace

### 4.2 Script types และกฎการรัน

| ชนิด | รันบน | วางได้ที่ (ตาม official locations doc) |
|---|---|---|
| `Script` (RunContext = Legacy) | Server | ต้องอยู่ใน server container: Workspace, ServerScriptService |
| `Script` (RunContext = Server) | Server | วางเพิ่มได้ที่ ReplicatedStorage แต่**ไม่แนะนำ** (ถูก replicate ไป client) |
| `Script` (RunContext = Client) | Client | ReplicatedStorage, StarterCharacterScripts, StarterPlayerScripts (วางที่ไหนก็ได้ + ตั้ง RunContext) |
| `LocalScript` | Client (เครื่องของผู้เล่น) | `StarterPlayer` (StarterPlayerScripts/StarterCharacterScripts), `StarterGui`, `StarterPack`, หรือใต้ Player (PlayerGui/Backpack/PlayerScripts) — จะถูก copy ให้ player ตอนเข้าเกม/เกิด; **ไม่รัน** จาก Workspace, ServerScriptService, ServerStorage, ReplicatedStorage |
| `ModuleScript` | ทั้งสองฝั่ง (require) | ที่ไหนก็ได้ เช่น ReplicatedStorage สำหรับ shared |

- `game:GetService(...)` ใช้ได้ทั้ง 2 ฝั่ง แต่ `DataStoreService`, `HttpService:GetAsync` ฯลฯ ทำงานเฉพาะ server
- `script.Parent` ใน LocalScript ที่อยู่ใต้ StarterPack/StarterGui จะชี้ไปที่ instance ที่ถูก clone ลง player แล้ว
- loading screen ตาม official: วาง `Script` (RunContext = Client) ใน ReplicatedFirst (replicate ถึง client ก่อนทุกอย่าง) — official แนะนำ Script+RunContext ไม่ใช่ LocalScript

### 4.3 Client-server หลักการบังคับ

```
Client (LocalScript)          Server (Script)
   user input  ──FireServer──▶  validate arguments
   (ฟังด้วย)  ◀──FireClient────  ตัดสินใจ / เปลี่ยน state
```

- **RemoteEvent**: ยิงแล้วไม่รอผล (`:FireServer()`, `:FireClient(player, ...)`, `:FireAllClients(...)`)
- **RemoteFunction**: ต้องรอผล (`:InvokeServer()` / `:OnServerInvoke` — ระวัง yield จน timeout ถ้า server ผิดพลาด)
- **BindableEvent/BindableFunction**: ใช้คุยกัน**ภายในฝั่งเดียว** (server↔server หรือ client↔client) ห้ามใช้ข้ามฝั่ง
- กฎ authority: **client ขอ → server ตัดสินใจ** เงิน/ดาเมจ/ไอเทม/คะแนน ต้องเปลี่ยนฝั่ง server แล้ว replicate กลับ ห้าม server ไว้ใจ argument จาก client — validate ทุกค่าทั้งชนิดและช่วง (ดู Security checklist)
- Remote ทั้งหมดควรสร้างฝั่ง server แล้ววางใน ReplicatedStorage (หรือผ่าน wrapper module — ดู template B5)

### 4.4 Datatypes หลักและกับดัก

| Type | ใช้กับ | กับดักที่เจอบ่อย |
|---|---|---|
| `Vector3.new(x, y, z)` | ตำแหน่ง/ขนาด | `Position` ต้อง assign `Vector3` ทั้งก้อน ไม่ใช่แค่ตัวเลข |
| `CFrame.new(pos)` / `CFrame.lookAt(from, to)` | ตำแหน่ง + ทิศทาง | `CFrame.Angles(...)` รับค่า**เรเดียน** แต่ `Grip.Orientation`, `Part.Orientation` เป็น**องศา** — อย่าสลับ |
| `UDim2.new(scale, offset, scale, offset)` | UI ตำแหน่ง/ขนาด | UI ต้องใช้ UDim2 เสมอ และออกแบบ responsive ด้วย Scale (% ของ parent) ไม่ใช่ Offset (px) ล้วน |
| `Color3.fromRGB(r, g, b)` | สี | `Color3.new()` รับ 0-1 ไม่ใช่ 0-255 |
| `TweenInfo.new(duration, easingStyle, easingDirection)` | animation | ใช้กับ `TweenService:Create(target, tweenInfo, goal)` เท่านั้น ห้ามตั้ง property ใน loop |
| `Enum.*` | ค่าคงที่ | ตรวจว่าค่า enum ถูกต้องก่อน assign (เช่น `Enum.Material.SmoothPlastic`) |

### 4.5 Script Sync file conventions

Roblox Studio sync สคริปต์ไปเป็นไฟล์บนดิสก์ (แก้จาก opencode/VS Code ได้ แล้ว Studio อัปเดตอัตโนมัติ):

| ไฟล์บนดิสก์ | Instance ใน Studio |
|---|---|
| `name.luau` | ModuleScript |
| `name.server.luau` | Script (RunContext = Server) |
| `name.client.luau` | Script (RunContext = Client) |
| `name.local.luau` | LocalScript |
| `name.legacy.luau` | Script (RunContext = Legacy) |
| `name.plugin.luau` | Script (RunContext = Plugin) |
| `name/` (directory) | Folder |
| `name/init.*.luau` | script ที่มีลูก (ตาม type ข้างบน) |

ข้อควรจำ: sync เฉพาะ Script/LocalScript/ModuleScript/Folder — ห้ามใส่ attributes/tags บน script ที่ sync (โดน ignore เสี่ยงข้อมูลหาย), ชื่อไฟล์ต้องเข้ากันได้กับ filesystem

## 5. Tools Templates

อิงจาก official doc: create.roblox.com/docs/players/tools — ในเกม Roblox ใช้ instance **`Tool`** เป็นฐานของทุก tool (อาวุธ, ไม้กายสิทธิ์, ไฟฉาย, กุญแจ, ไอเทมสะสม)

### A1 — สร้าง Tool: physical vs non-physical

ขั้นตอนใน Studio:
1. Explorer → คลิก ⊕ ที่ `Workspace` → insert `Tool`
2. เลือก Tool → Properties → ตั้ง `TextureId` (ไอคอนใน inventory) และ `ToolTip` (ชื่อตอน hover)
3. เลือกว่าเป็น physical หรือ non-physical:

**Physical tool** (เห็นในโลก 3D): ต้องมี **part ชิ้นเดียวชื่อ `Handle`** (R15 จะติดกับ RightHand, R6 ติดกับ Right Arm) — ถ้า tool หลายชิ้น ให้ใช้ `WeldConstraint` เชื่อมทุกชิ้นเข้ากับ Handle
- ⚠️ **ห้าม `Anchored = true`** บนชิ้นส่วน tool — เดี๋ยวตัวละครติดกับที่ตอนหยิบ/สวม ไม่งั้นต้องใส่สคริปต์ unanchor (ดู A3)
- ⚠️ ต้องมี Handle **แค่ชิ้นเดียว** — ถ้ามีหลายชิ้น engine จะสุ่มจับตำแหน่ง ตัวละครอาจถือใบดาบแทนด้าม

**Non-physical tool** (มีแค่ใน inventory, กดใช้ได้เลย เช่น คาถา): ตั้ง `RequiresHandle = false` — ไม่ต้องมี Handle/3D composition

### A2 — ปรับ Grip (ท่าถือ)

`Grip` เป็น CFrame กำหนดว่า tool อยู่ตรงไหน/หันทิศไหน เทียบกับมือตัวละคร — ต้องลองค่าจริงใน Studio:
- `Grip.Position` = Vector3 offset เช่น `(0, -1.5, 0)` — คบเพลิงห้อยข้างตัว
- `Grip.Orientation` = **องศา** เช่น `(25, 0, 0)` — คบเพลิงเอียงให้ไม่ลากพื้น
- ตัวอย่างผิด: `(0, -2, 0)` + `(-90, 0, 0)` → คบเพลิงลากพื้น; ตัวอย่างถูก: `(0, -1.5, 0)` + `(25, 0, 0)` → คบเพลิงอยู่ข้างหน้าตัว

### A3 — วาง Tool ไว้ที่ไหน (3 ตำแหน่ง)

| จุดประสงค์ | วางที่ | หมายเหตุ |
|---|---|---|
| ทุกคนเกิดมาได้ใน inventory | `StarterPack` | copy อัตโนมัติไป `Backpack` ตอน spawn; เรียงลำดับตามที่เพิ่มเข้า — ต้องการควบคุมลำดับ ดู "Reorder Inventory" ด้านล่าง; ควบคุมรายละเอียดรายคนด้วย `StarterGear` |
| Pickup เก็บได้ในโลก | `Workspace` | engine จัดการ collision ให้อัตโนมัติ — พอตัวละครชน จะสวม + เก็บเข้าคลังเอง; ถ้าต้องการให้ tool ลอยนิ่ง ให้ `Anchored` + ใส่สคริปต์ unanchor (โค้ดล่าง) |
| รางวัล/ซื้อจากร้าน | `ReplicatedStorage` | สคริปต์ฝั่ง server `Clone()` ไป `Backpack` ตามเวลาที่เหมาะสม |

**Unanchor on equip/collect** (ใส่ใน Tool ที่ Anchored):
```luau
local tool = script.Parent

tool.Equipped:Connect(function()
	for _, part in tool:GetDescendants() do
		if part:IsA("BasePart") and part.Anchored then
			part.Anchored = false
		end
	end
end)
```

**Clone tool ไป inventory ของผู้เล่น** (ฝั่ง server):
```luau
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

Players.PlayerAdded:Connect(function(player)
	-- เช่น แจกตอนทำภารกิจสำเร็จ (จริงๆ ควรอยู่ในฟังก์ชันให้รางวัล)
	local backpack = player:WaitForChild("Backpack")
	local tool = ReplicatedStorage:FindFirstChild("WaterSpell")
	if tool then
		tool:Clone().Parent = backpack
	end
end)
```

**Reorder inventory** (LocalScript ใน StarterPack):
```luau
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local order = {"WaterSpell", "FireSpell", "AirSpell", "EarthSpell"}
local backpack = script.Parent

for _, item in backpack:GetChildren() do
	if item:IsA("Tool") then
		item.Parent = ReplicatedStorage
	end
end

for i = 1, #order do
	local tool = ReplicatedStorage:FindFirstChild(order[i])
	if tool then
		tool.Parent = backpack
	end
end
```

### A4 — Tool events (4 ตัว)

Event พวกนี้**รันเฉพาะ client** (LocalScript) เพราะ input อยู่ที่เครื่องผู้เล่น → งานฝั่ง server ต้องส่งผ่าน RemoteEvent:

| Event | ยิงเมื่อ |
|---|---|
| `Equipped` | ผู้เล่นเลือก tool จาก backpack |
| `Unequipped` | สลับ tool หรือทิ้ง tool |
| `Activated` | เริ่มกดใช้ (คลิก/แตะ/gamepad trigger ขวา) |
| `Deactivated` | ปล่อยปุ่มใช้ |

```luau
local tool = script.Parent

local function onEquip()
	print(tool.Name, "tool equipped")
end
local function onUnequip()
	print(tool.Name, "tool unequipped")
end
local function onActivate()
	print(tool.Name, "tool activated")
end
local function onDeactivate()
	print(tool.Name, "tool deactivated")
end

tool.Equipped:Connect(onEquip)
tool.Unequipped:Connect(onUnequip)
tool.Activated:Connect(onActivate)
tool.Deactivated:Connect(onDeactivate)
```

### A5 — ตัวอย่าง tool จริง (client ตรวจจับ → server ลงมือ)

**Creator's Wand** — คลิกที่ไหน server สร้าง part ตรงนั้น (client หาว่าเล็งอะไร, server สร้างของ):
```luau
-- LocalScript (ใน Tool)
local tool = script.Parent
local remotes = require(game.ReplicatedStorage.Remotes)

tool.Activated:Connect(function()
	local mouse = game.Players.LocalPlayer:GetMouse()
	remotes.SpawnBlock:FireServer(mouse.Hit)
end)
```
```luau
-- Script (ServerScriptService)
local remotes = require(game.ReplicatedStorage.Remotes)

remotes.SpawnBlock.OnServerEvent:Connect(function(player, hitCFrame)
	if typeof(hitCFrame) ~= "CFrame" then
		return -- reject: validate argument
	end
	local part = Instance.new("Part")
	part.Size = Vector3.new(1, 1, 1)
	part.CFrame = hitCFrame
	part.Parent = workspace
end)
```

**Invisibility Cloak** — สวมแล้วหายตัวจากทุกคน, ถอดแล้วกลับมา (server เปลี่ยน visibility):
```luau
-- LocalScript (ใน Tool)
local tool = script.Parent
local remotes = require(game.ReplicatedStorage.Remotes)

tool.Equipped:Connect(function()
	remotes.Cloak:FireServer(true)
end)
tool.Unequipped:Connect(function()
	remotes.Cloak:FireServer(false)
end)
```
```luau
-- Script (ServerScriptService)
local remotes = require(game.ReplicatedStorage.Remotes)

remotes.Cloak.OnServerEvent:Connect(function(player, isCloaked)
	if type(isCloaked) ~= "boolean" then
		return
	end
	local character = player.Character
	if not character then
		return
	end
	-- ตรวจว่า tool อยู่ในมือจริง (กัน exploit ยิง FireServer เปล่าๆ)
	local equippedTool = character:FindFirstChildOfClass("Tool")
	if not equippedTool or equippedTool.Name ~= "InvisibilityCloak" then
		return
	end
	-- server เป็นเจ้าของ state: Transparency ที่ตั้งบน server จะ replicate ไปทุกคน
	for _, part in character:GetDescendants() do
		if part:IsA("BasePart") and part.Name ~= "Handle" then
			part.Transparency = isCloaked and 1 or 0
		end
	end
end)
```

**Mega-Bow** — กดค้างนาน = ยิงแรง (client วัดเวลา Activated→Deactivated, server ยิง):
```luau
-- LocalScript (ใน Tool)
local tool = script.Parent
local remotes = require(game.ReplicatedStorage.Remotes)

local chargeStart = 0

tool.Activated:Connect(function()
	chargeStart = os.clock()
end)
tool.Deactivated:Connect(function()
	local chargeTime = os.clock() - chargeStart
	remotes.ShootArrow:FireServer(chargeTime)
end)
```
```luau
-- Script (ServerScriptService)
local remotes = require(game.ReplicatedStorage.Remotes)

remotes.ShootArrow.OnServerEvent:Connect(function(player, chargeTime)
	if type(chargeTime) ~= "number" then
		return
	end
	local power = math.clamp(chargeTime, 0, 3) -- กันค่ามั่วจาก client
	-- ... ยิงลูกศรด้วยแรงตาม power (สร้าง projectile ใน workspace)
end)
```

## 6. Systems Templates

### B1 — PlayerData module + DataStore (ฝั่ง server)

Pattern พื้นฐาน: โหลดด้วย `pcall` + retry, กัน 2 server เขียนพร้อมกันด้วย key, auto-save + save ตอนออกจากเกม

```luau
-- ReplicatedStorage/PlayerDataService.server.luau (Script, RunContext=Server)
local Players = game:GetService("Players")
local DataStoreService = game:GetService("DataStoreService")

local Store = DataStoreService:GetDataStore("PlayerDataV1")
local sessions = {} -- player -> { data = {...} }

local DEFAULT_DATA = {
	Coins = 0,
	Gems = 0,
	Level = 1,
}

local function loadData(player)
	local key = "Player_" .. player.UserId
	for attempt = 1, 3 do
		local ok, result = pcall(function()
			return Store:GetAsync(key)
		end)
		if ok then
			local data = result or {}
			-- merge กับค่า default เผื่อ schema เปลี่ยน
			local merged = {}
			for field, defaultValue in DEFAULT_DATA do
				merged[field] = data[field] ~= nil and data[field] or defaultValue
			end
			return merged
		end
		task.wait(2 * attempt) -- backoff ก่อน retry
	end
	error("DataStore load failed for " .. player.Name)
end

local function savePlayer(player, data)
	local key = "Player_" .. player.UserId
	pcall(function()
		Store:UpdateAsync(key, function()
			return data
		end)
	end)
end

Players.PlayerAdded:Connect(function(player)
	local ok, data = pcall(loadData, player)
	if not ok then
		player:Kick("โหลดข้อมูลไม่สำเร็จ กรุณาลองใหม่")
		return
	end
	local session = { data = data }
	sessions[player] = session
	-- leaderstats ตาม B2
end)

Players.PlayerRemoving:Connect(function(player)
	local session = sessions[player]
	if session then
		savePlayer(player, session.data)
		sessions[player] = nil
	end
end)

game:BindToClose(function()
	for player, session in sessions do
		savePlayer(player, session.data)
	end
end)
```

### B2 — Leaderboard (leaderstats — ฝั่ง server เท่านั้น)

```luau
-- ต่อจาก B1 ภายใน PlayerAdded
local function setupLeaderboard(player, data)
	local leaderstats = Instance.new("Folder")
	leaderstats.Name = "leaderstats"
	leaderstats.Parent = player

	local coins = Instance.new("IntValue")
	coins.Name = "Coins"
	coins.Value = data.Coins
	coins.Parent = leaderstats
end
```

### B3 — Pickup ด้วย ProximityPrompt

```luau
-- ใน Script (ฝั่ง server) ที่เป็นแม่ของ part ที่มี ProximityPrompt
local prompt = script.Parent.ProximityPrompt

prompt.Triggered:Connect(function(player)
	local character = player.Character
	if character then
		local backpack = player:WaitForChild("Backpack")
		local tool = game.ReplicatedStorage:FindFirstChild("HealthPotion")
		if tool then
			tool:Clone().Parent = backpack
		end
		script.Parent:Destroy() -- หายไปหลังเก็บ
	end
end)
```

### B4 — UI พื้นฐาน (responsive + tween)

```luau
-- StarterGui/ShopUI.client.luau (LocalScript ใต้ StarterGui)
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ShopUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.fromScale(0.4, 0.5) -- 40% x 50% ของจอ (responsive)
frame.Position = UDim2.fromScale(0.3, 0.25)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
frame.Parent = screenGui

local buyButton = Instance.new("TextButton")
buyButton.Size = UDim2.fromScale(0.6, 0.2)
buyButton.Position = UDim2.fromScale(0.2, 0.7)
buyButton.Text = "ซื้อดาบ 50 เหรียญ"
buyButton.Parent = frame

-- เริ่มจากซ่อน แล้วค่อยแสดงตอนสปอว์น
local hiddenPos = UDim2.fromScale(-0.6, 0.25)
local shownPos = UDim2.fromScale(0.3, 0.25)
frame.Position = hiddenPos

local function showUI()
	frame:TweenPosition(shownPos, 0.4, Enum.EasingDirection.Out, Enum.EasingStyle.Quad)
end

buyButton.Activated:Connect(function()
	game.ReplicatedStorage.Remotes.BuyTool:FireServer("Sword")
end)

-- แสดงทุกครั้งที่สปอว์น (CharacterAdded ยิงทั้งเกิดครั้งแรกและ respawn)
if player.Character then
	showUI() -- เกิดอยู่แล้วก่อน script รัน
end
player.CharacterAdded:Connect(function(character)
	showUI()
end)
```

### B5 — Remote wrapper module (กันชื่อผิด + จัดการ centralized)

```luau
-- ReplicatedStorage/Remotes.luau (ModuleScript — ฝั่ง server ควรสร้างไว้ล่วงหน้า)
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local RemoteEvents = {}

local function ensureRemote(name)
	local remote = ReplicatedStorage:FindFirstChild(name)
	if not remote then
		remote = Instance.new("RemoteEvent")
		remote.Name = name
		remote.Parent = ReplicatedStorage
	end
	return remote
end

local Remotes = {
	SpawnBlock = ensureRemote("SpawnBlock"),
	Cloak = ensureRemote("Cloak"),
	ShootArrow = ensureRemote("ShootArrow"),
	BuyTool = ensureRemote("BuyTool"),
}

return Remotes
```

## 7. Review Checklist (ตรวจโค้ด Roblox ทุกครั้ง)

ก่อนส่งโค้ดให้ผู้ใช้ ให้เช็คตามนี้ — เขียนเครื่องหมาย ✓ หรือ ⚠️ ที่จุดที่ยังต้องแก้:

### Performance
- [ ] ไม่มีงานต่อ frame ที่ไม่จำเป็น (`RunService.Heartbeat` = สุดท้ายที่ควรใช้; animation/UI ใช้ `TweenService`)
- [ ] ไม่สร้าง instance/table ใหม่ซ้ำใน loop หรือใน event ที่ยิงถี่
- [ ] `GetDescendants()`/`GetChildren()` ไม่ถูกเรียกซ้ำๆ ใน loop — cache ไว้ก่อน
- [ ] Event connections ทุกตัวมีทาง disconnect (`RBXScriptConnection:Disconnect()` หรือ auto เมื่อ instance ตาย) ไม่มี leak
- [ ] ใช้ `task.wait()`/`task.spawn()`/`task.defer()` (ตัวแนะนำของ Roblox; coroutine ยังใช้ได้แต่ไม่ต้อง), และระวัง yield ใน loop (`while true do` ต้องมี wait)
- [ ] สคริปต์ฝั่ง client ไม่ทำ server work ซ้ำ (เช่น ตรวจ collision ซ้ำ)

### Security (server authority)
- [ ] เงิน/ดาเมจ/ไอเทม/คะแนน ถูกเปลี่ยนฝั่ง server เท่านั้น
- [ ] ทุก argument จาก RemoteEvent ถูก validate ทั้ง **ชนิด** (`type()`/`typeof()`) และ**ช่วงค่า** (`math.clamp`) ก่อนใช้งาน
- [ ] ไม่มี `FireServer` ที่ server ไว้ใจโดยไม่ตรวจ (เช่น ส่งตำแหน่งมายิง แล้ว server สร้างของตรงนั้น — ตรวจว่า CFrame อยู่ในระยะ/บริเวณที่สมเหตุสมผล)
- [ ] ไม่มีข้อมูลลับ (API key, รหัส) อยู่ใน ReplicatedStorage/Workspace/LocalScript
- [ ] ไม่ใช้ `LoadLibrary` (deprecated) และระวัง third-party packages/plugins ที่ไม่น่าเชื่อถือ

### Style / Best practices
- [ ] ตัวแปรทุกตัวเป็น `local` (ไม่มี global หลุด)
- [ ] ใช้ ModuleScript แยก logic ที่ใช้ร่วมกัน (ไม่ copy-paste โค้ด)
- [ ] ตั้งชื่ออ่านเข้าใจ: `PlayerDataService`, `Remotes`, `SwordTool` (PascalCase สำหรับ instance/class, camelCase สำหรับฟังก์ชัน/ตัวแปร)
- [ ] มี type annotation บน parameter ของฟังก์ชันสำคัญ (Luau ใช้ gradual typing ได้)
- [ ] ใช้ `game:GetService(...)` ทุกครั้ง ไม่ใช้ global shortcut (`game.Workspace` → `game:GetService("Workspace")`)
- [ ] โค้ด conform stylua format (indent 1 tab, single quotes)

## 8. Coverage Map — ครอบคลุมทุกหมวดที่ "เขียนได้" ใน docs

Skill นี้ embed template เฉพาะระบบที่พบบ่อยที่สุด (~8 ระบบ) — docs ของ Roblox มีเนื้อหาที่เขียนสคริปต์ได้ ~25 หมวดใหญ่ **ห้ามเขียน API จากความจำ**: ถ้า feature ที่ผู้ใช้ขอไม่อยู่ใน section ข้างบน ให้ **webfetch เอกสาร official ก่อนเขียนโค้ดเสมอ** (ทุก URL ในตารางนี้ผ่านการยืนยันจาก `llms.txt` แล้ว):

- ดัชนี docs ทั้งหมด (ทุกหน้าเป็น markdown): `https://create.roblox.com/docs/llms.txt`
- ดัชนี Engine API ทั้งหมด (Luau, ทุกคลาส/เมธอด): `https://create.roblox.com/docs/reference/engine/llms.txt`
- หน้าบทความแต่ละหัวข้อ (ลงท้าย `.md` ได้เลย): `https://create.roblox.com/docs/en-us/<path>.md`

### หมวด: Input และ Camera
| หัวข้อ | เปิดที่ |
|---|---|
| Input Action System (แนะนำสำหรับปุ่ม/การบังคับ) | https://create.roblox.com/docs/en-us/input/input-action-system |
| เมาส์ + คีย์บอร์ด | https://create.roblox.com/docs/en-us/input/mouse-and-keyboard |
| Mobile (touch) | https://create.roblox.com/docs/en-us/input/mobile |
| Gamepad | https://create.roblox.com/docs/en-us/input/gamepad |
| Camera (ปรับกล้อง, lock, FOV) | https://create.roblox.com/docs/en-us/workspace/camera |

### หมวด: Character / Player
| หัวข้อ | เปิดที่ |
|---|---|
| Character appearance / avatar | https://create.roblox.com/docs/en-us/characters/appearance |
| Health/ชื่อผู้เล่นเหนือหัว (Humanoid) | https://create.roblox.com/docs/en-us/characters/name-health-display |
| Emotes | https://create.roblox.com/docs/en-us/characters/emotes |
| Character Controller Library (CCL) | https://create.roblox.com/docs/en-us/characters/character-controller-library |
| Pathfinding (NPC เดินหาเส้นทาง) | https://create.roblox.com/docs/en-us/characters/pathfinding |
| Loading screen | https://create.roblox.com/docs/en-us/players/loading-screens |
| Avatar Editor / inventory ผู้เล่น | https://create.roblox.com/docs/en-us/players/avatar-editor |

### หมวด: Environment / Effects
| หัวข้อ | เปิดที่ |
|---|---|
| Global lighting | https://create.roblox.com/docs/en-us/environment/lighting |
| Atmosphere / Clouds / Wind | https://create.roblox.com/docs/en-us/environment/atmosphere |
| Post-processing | https://create.roblox.com/docs/en-us/environment/post-processing-effects |
| Skybox | https://create.roblox.com/docs/en-us/environment/skybox |
| Light sources (Point/Spot/Surface) | https://create.roblox.com/docs/en-us/effects/light-sources |
| ParticleEmitter (ไฟ/ควัน/ประกาย) | https://create.roblox.com/docs/en-us/effects/particle-emitters |
| Beams / Trails / Highlight | https://create.roblox.com/docs/en-us/effects/beams |

### หมวด: Physics / 3D World
| หัวข้อ | เปิดที่ |
|---|---|
| Parts / Meshes / Models / Materials | https://create.roblox.com/docs/en-us/parts |
| Physics ภาพรวม (assemblies, network ownership) | https://create.roblox.com/docs/en-us/physics |
| Mechanical constraints (Hinge/Spring/Rope/Weld ฯลฯ) | https://create.roblox.com/docs/en-us/physics/mechanical-constraints |
| Mover constraints (LinearVelocity/AlignPosition ฯลฯ) | https://create.roblox.com/docs/en-us/physics/mover-constraints |
| CFrame / ตำแหน่งทิศทาง | https://create.roblox.com/docs/en-us/workspace/cframes |
| Collision / Raycasting | https://create.roblox.com/docs/en-us/workspace/raycasting |

### หมวด: UI
| หัวข้อ | เปิดที่ |
|---|---|
| UI ภาพรวม + on-screen containers | https://create.roblox.com/docs/en-us/ui/on-screen-containers |
| ScrollingFrame | https://create.roblox.com/docs/en-us/ui/scrolling-frames |
| ViewportFrame (แสดง 3D ใน UI) | https://create.roblox.com/docs/en-us/ui/viewport-frames |
| TextInput / ปุ่ม / ลาก UI (drag detectors) | https://create.roblox.com/docs/en-us/ui/text-input |
| Layout (List/Grid/Flex) | https://create.roblox.com/docs/en-us/ui/list-flex-layouts |
| Styling / Rich text / Text filtering | https://create.roblox.com/docs/en-us/ui/styling |
| UI animation (tween) / 9-slice | https://create.roblox.com/docs/en-us/ui/animation |

### หมวด: ระบบเกม
| หัวข้อ | เปิดที่ |
|---|---|
| Teams (ทีมแดง-น้ำเงิน) | https://create.roblox.com/docs/en-us/players/teams |
| Teleport ระหว่าง place | https://create.roblox.com/docs/en-us/projects/teleport |
| Badges | https://create.roblox.com/docs/en-us/production/publishing/badges |
| Matchmaking | https://create.roblox.com/docs/en-us/matchmaking |
| Instance streaming (โหลด 3D เฉพาะโซน) | https://create.roblox.com/docs/en-us/workspace/streaming |
| ซื้อไอเทม/Gamepass/สินค้า (MarketplaceService) | https://create.roblox.com/docs/en-us/monetize-experiences |

### หมวด: Data / Cloud services
| หัวข้อ | เปิดที่ |
|---|---|
| DataStores (ละเอียด: versioning/errors/limits) | https://create.roblox.com/docs/en-us/cloud-services/data-stores |
| MemoryStore (ข้อมูลใน RAM ข้าม server) | https://create.roblox.com/docs/en-us/cloud-services/memory-stores |
| ส่งข้อความข้าม server (MessagingService) | https://create.roblox.com/docs/en-us/cloud-services/cross-server-messaging |
| HTTP requests (HttpService) | https://create.roblox.com/docs/en-us/cloud-services/http-service |
| Secrets store (API key ปลอดภัย) | https://create.roblox.com/docs/en-us/cloud-services/secrets |

### หมวด: สื่อ / เสียง / แชท / Animation
| หัวข้อ | เปิดที่ |
|---|---|
| Audio objects / effects / 2D-3D sound | https://create.roblox.com/docs/en-us/audio/objects |
| Animation (เล่นผ่าน Animator) | https://create.roblox.com/docs/en-us/animation/using |
| Chat (TextChatService + custom commands) | https://create.roblox.com/docs/en-us/chat/in-experience-text-chat |

### หมวด: Scripting ขั้นสูง / Security / Performance
| หัวข้อ | เปิดที่ |
|---|---|
| Debounce (กันยิง event ซ้ำ) | https://create.roblox.com/docs/en-us/scripting/debounce |
| Scheduler (task ต่อ frame) | https://create.roblox.com/docs/en-us/scripting/scheduler |
| Parallel Luau (multithreading) | https://create.roblox.com/docs/en-us/scripting/multithreading |
| Security tactics / client-server boundary | https://create.roblox.com/docs/en-us/scripting/security/security-tactics |
| Performance: improve / MicroProfiler | https://create.roblox.com/docs/en-us/performance-optimization/improve |
| Localization (หลายภาษา) | https://create.roblox.com/docs/en-us/production/localization |

### หมวด: Tooling
| หัวข้อ | เปิดที่ |
|---|---|
| File-based workflow (Rojo ฯลฯ) | https://create.roblox.com/docs/en-us/projects/external-tools |
| Roblox Studio MCP (เชื่อมต่อ AI/IDE กับ Studio) | https://create.roblox.com/docs/en-us/studio/mcp |
| Script Sync | https://create.roblox.com/docs/en-us/scripting/sync |

กติกา: ไม่รู้ → เปิด docs ก่อนเขียน ไม่มีข้อยกเว้น — ถ้าหัวข้อไม่อยู่ในตาราง ให้ค้นจาก `https://create.roblox.com/docs/llms.txt`
