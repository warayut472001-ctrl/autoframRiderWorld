-- AdminAllInOne.lua v5.1 - ULTRA MODERN UI + MULTI-DUNGEON SYSTEM + CRONUS BOSS!
-- 🎨 NEW: Completely Redesigned UI with Modern Aesthetics
-- 🌈 Gradient Colors, Smooth Animations, Glassmorphism Effects
-- ⚡ NEW: Added Cronus Lv.90 Boss Farm

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
-- 📱 Detect Mobile
local isMobile = UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled
local RunService = game:GetService("RunService")
local workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TweenService = game:GetService("TweenService")

-- ตัวแปรควบคุม Auto Attack/Skills 
local combatPaused = false

local player = Players.LocalPlayer

-- =================== Settings ===================
local programRunning = true
local livesFolderName = "Lives"

-- =================== Auto Level Farm Database ===================
local levelFarmData = {
    {level = 1, maxLevel = 4, monster = "Lost Rider Lv.1"},
    {level = 5, maxLevel = 6, monster = "Armed Lost Rider Lv.5"},
    {level = 7, maxLevel = 9, monster = "Dragon User Lv.7"},
    {level = 10, maxLevel = 11, monster = "Crab User Lv.10"},
    {level = 12, maxLevel = 14, monster = "Bat User Lv.12"},
    {level = 15, maxLevel = 15, monster = "Bull User Lv.15"},
    {level = 16, maxLevel = 19, monster = "Foundation Soldier Lv.16"},
    {level = 20, maxLevel = 24, monster = "Cobra User Lv.20"},
    {level = 25, maxLevel = 29, monster = "Manta User Lv.25"},
    {level = 30, maxLevel = 34, monster = "Swan User Lv.30"},
    {level = 35, maxLevel = 39, monster = "Zebra Monster Lv.35"},
    {level = 40, maxLevel = 44, monster = "Dark Dragon User Lv.40"},
    {level = 45, maxLevel = 49, monster = "Gazelle User Lv.45"},
    {level = 50, maxLevel = 54, monster = "Violent Dragoon Lv.50"},
    {level = 55, maxLevel = 59, monster = "Shark Overloaded Lv.55"},
    {level = 60, maxLevel = 63, monster = "Shark User Lv.60"},
    {level = 64, maxLevel = 69, monster = "Foundation Soldier Lv.64"},
    {level = 70, maxLevel = 74, monster = "Timeless Goon Lv.70"},
    {level = 75, maxLevel = 79, monster = "Captain Goon Lv.75"},
    {level = 80, maxLevel = 999, monster = "STOP"}
}

local targetMonsters = {
    "Lost Rider Lv.1","Armed Lost Rider Lv.5","Dragon User Lv.7",
    "Crab User Lv.10","Bat User Lv.12","Bull User Lv.15",
    "Foundation Soldier Lv.16","Cobra User Lv.20","Manta User Lv.25",
    "Swan User Lv.30","Zebra Monster Lv.35","Dark Dragon User Lv.40",
    "Gazelle User Lv.45","Violent Dragoon Lv.50","Shark Overloaded Lv.55",
    "Shark User Lv.60","Foundation Soldier Lv.64","Timeless Goon Lv.70",
    "Captain Goon Lv.75","Rushed Blaster Lv.90","Rushed Elasther Lv.90",
    "Rushed Orca Lv.90"
}

local selectedMonsters = {}
for _, m in ipairs(targetMonsters) do selectedMonsters[m] = true end

local state = {
    autoFarm=false, autoFarmInterval=0.1,
    autoLevelFarm=false,
    lockPos=false,
    afkEnabled=false,
    autoBoss=false,
    autoCronus=false,
    autoDungeon=false,
    selectedDungeon="ethernal",
    autoMine=false,
    autoAttack=false,
    autoHeavyAttack=false,
    autoKeyX=false,
    autoSkills={E=false, R=false, C=false, V=false},
    autoQuest=false,
    selectedQuest=nil,
    autoEvent=false,
    autoHeal=false,       
    autoHenshin=false      
}

-- =================== Quest Database ===================
local questDatabase = {
    {
        id = "quest_nocap",
        name = "No CAP!",
        npcName = "Ryuga",
        npcPath = {"NPC", "Ryuga"},
        dialogueSteps = {"[ Repeatable Quest ]"},
        monsters = {"Utsumi Lv.80", "Mad Isurugi Lv.80"},
        questUIName = "No CAP!",
        questType = "kill",
        icon = "⚔️"
    },
    {
        id = "quest_bullet_metal",
        name = "The Bullet & Metal",
        npcName = "ElementalResearcher",
        npcPath = {"NPC", "ElementalResearcher"},
        dialogueSteps = {"Hello!", "Yes, show me what you have", "The Bullet and Metal", "Start 'The Bullet and Metal'"},
        monsters = {"Metal Man Lv.55", "Bullet Man Lv.55"},
        questUIName = "The Bullet & Metal",
        questType = "kill",
        icon = "🔫"
    },
    {
        id = "quest_fire_luna",
        name = "The Fire & Luna",
        npcName = "ElementalResearcher",
        npcPath = {"NPC", "ElementalResearcher"},
        dialogueSteps = {"Hello!", "Yes, show me what you have", "The Fire and Luna", "Start 'The Fire and Luna'"},
        monsters = {"Luna Girl Lv.55", "Flare Man Lv.55"},
        questUIName = "The Fire & Luna",
        questType = "kill",
        icon = "🔥"
    },
    {
        id = "quest_agito",
        name = "Agito's Rules",
        npcName = "Shoichi",
        npcPath = {"NPC", "Shoichi"},
        dialogueSteps = {"[ Challenge ]", "[ Quest ]"},
        questUIName = "Agito's Rules",
        questType = "summon",
        summonLocation = {"KeyItem", "Spawn", "AgitoStone"},
        summonKey = "E",
        bossName = "Agito Lv.90",
        icon = "👹"
    },
    {
        id = "quest_ancient",
        name = "Ancient Argument",
        npcName = "DojoStudent",
        npcPath = {"NPC", "DojoStudent"},
        dialogueSteps = {"Sure..", "Ancient Argument", "Start 'Ancient Argument'"},
        questUIName = "Ancient Argument",
        questType = "ancient_dungeon",
        dungeonName = "Trial of Ancient",
        stonePositions = {
            {name = "หินที่ 1", cframe = CFrame.new(-2769.3479, 2.254, -1455.29102)},
            {name = "หินที่ 2", cframe = CFrame.new(-2878.06396, 2.254, -1304.30896)},
            {name = "หินที่ 3", cframe = CFrame.new(-2977.73804, 2.254, -1399.21997)}
        },
        bossNames = {"Daguba Lv.90", "Mighty Rider Lv.90", "Empowered Daguba Lv.90"},
        icon = "🏛️"
    },
{
        id = "quest_hunt_hunted",
        name = "The Hunt Hunted",
        npcName = "Malcom",
        npcPath = {"NPC", "Malcom"},
        dialogueSteps = {
            "I'm ready for the challenge!",
            "The Hunt Hunted",
            "Start 'The Hunt Hunted'"
        },
        monsters = {
            "Dark Dragon User Lv.40",
            "Gazelle User Lv.45"
        },
        questUIName = "The Hunt Hunted",
        questType = "kill",
        icon = "🎯"
    },
    
}

-- =================== Boss Database ===================
local bossDatabase = {
    ethernal = {
        id = "ethernal",
        displayName = "Trial of Ethernal",
        dungeonName = "Trial of Ethernal",
        bossNames = {"Ethernal Lv.90"},
        icon = "⚡"
    },
    ancient = {
        id = "ancient",
        displayName = "Trial of Ancient",
        dungeonName = "Trial of Ancient",
        bossNames = {"Daguba Lv.90", "Mighty Rider Lv.90", "Empowered Daguba Lv.90"},
        icon = "🏛️"
    },
    cronus = {
        id = "cronus",
        displayName = "Cronus Boss",
        bossName = "Cronus Lv.90",
        icon = "⏰"
    }
}

local bp,bv,bg
local vu = game:GetService("VirtualUser")
player.Idled:Connect(function()
    if state.afkEnabled then
        pcall(function()
            vu:CaptureController()
            vu:ClickButton2(Vector2.new(0,0))
        end)
    end
end)

-- =================== Helper Functions ===================
local function getChar() return player.Character or player.CharacterAdded:Wait() end
local function getHRP() return getChar():FindFirstChild("HumanoidRootPart") end

local function getPlayerLevel()
    local success, level = pcall(function()
        local stats = player:FindFirstChild("StatsReplicated")
        if stats then
            local levelValue = stats:FindFirstChild("Level")
            if levelValue then
                return levelValue.Value
            end
        end
        return 1
    end)
    
    if success and level and level > 0 then
        return level
    else
        return 1
    end
end

local function getTargetMonsterByLevel(level)
    for _, data in ipairs(levelFarmData) do
        if level >= data.level and level <= data.maxLevel then
            return data.monster
        end
    end
    return nil
end

local function findMonsterByName(monsterName)
    local livesFolder = workspace:FindFirstChild(livesFolderName)
    if not livesFolder then return nil end
    
    for _, mob in ipairs(livesFolder:GetChildren()) do
        if mob.Name == monsterName and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") then
            if mob.Humanoid.Health > 0 then
                return mob
            end
        end
    end
    return nil
end

local function smartTeleport(targetCFrame)
    local hrp = getHRP()
    if not hrp or not targetCFrame then 
        return false 
    end
    
    local char = getChar()
    
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
    
    for i = 1, 10 do
        pcall(function()
            hrp.CFrame = targetCFrame
            hrp.Velocity = Vector3.new(0, 0, 0)
            hrp.RotVelocity = Vector3.new(0, 0, 0)
        end)
        wait(0.03)
    end
    
    wait(0.2)
    
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" and part.Name ~= "Head" then
            part.CanCollide = true
        end
    end
    
    return true
end

local function getNearbyMonsters()
    local mobs = {}
    local livesFolder = workspace:FindFirstChild(livesFolderName)
    if livesFolder then
        for _, mob in ipairs(livesFolder:GetChildren()) do
            if selectedMonsters[mob.Name] and mob:FindFirstChild("HumanoidRootPart") then
                table.insert(mobs, mob)
            end
        end
    end
    local hrp = getHRP()
    if hrp then
        table.sort(mobs,function(a,b)
            return (hrp.Position - a.HumanoidRootPart.Position).Magnitude < (hrp.Position - b.HumanoidRootPart.Position).Magnitude
        end)
    end
    return mobs
end

local function getBoss()
    local livesFolder = workspace:FindFirstChild(livesFolderName)
    if not livesFolder then return nil end
    for _, mob in ipairs(livesFolder:GetChildren()) do
        if mob.Name == "Possessed Rider Lv.90" and mob:FindFirstChild("HumanoidRootPart") then
            return mob
        end
    end
    return nil
end

-- ⚡ NEW: ฟังก์ชันสำหรับหา Cronus Boss
local function getCronusBoss()
    local livesFolder = workspace:FindFirstChild(livesFolderName)
    if not livesFolder then return nil end
    for _, mob in ipairs(livesFolder:GetChildren()) do
        if mob.Name == "Cronus Lv.90" and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") then
            if mob.Humanoid.Health > 0 then
                return mob
            end
        end
    end
    return nil
end

local function clickNPC(npc)
    if not npc then return false end
    local success = false
    pcall(function()
        for _, obj in pairs(npc:GetDescendants()) do
            if obj:IsA("ClickDetector") then
                if fireclickdetector then
                    fireclickdetector(obj)
                    success = true
                    break
                end
            end
        end
        if not success then
            for _, obj in pairs(npc:GetDescendants()) do
                if obj:IsA("ProximityPrompt") then
                    if fireproximityprompt then
                        fireproximityprompt(obj)
                        success = true
                        break
                    end
                end
            end
        end
        if not success then
            for _, obj in pairs(npc:GetDescendants()) do
                if obj:IsA("ClickDetector") then
                    obj.MouseClick:Fire(player)
                    success = true
                    break
                end
            end
        end
    end)
    return success
end

local function getNPCFromPath(path)
    local current = workspace
    for _, part in ipairs(path) do
        current = current:FindFirstChild(part)
        if not current then return nil end
    end
    return current
end

-- =================== 🚀 SMART QUEST SYSTEM (วาปแค่ครั้งเดียว แล้วรับเควสเร็ว!) ===================
local function smartWarpToNPC(questData)
    local npc = getNPCFromPath(questData.npcPath)
    if not npc or not npc:FindFirstChild("HumanoidRootPart") then
        print("❌ [Quest] ไม่พบ NPC: " .. questData.npcName)
        return false
    end
    
    local hrp = getHRP()
    if not hrp then return false end
    
    print("🚀 [Quest] วาปไป NPC: " .. questData.npcName)
    
    -- วาปไปหา NPC แบบเร็ว (แค่ครั้งเดียว!)
    local npcCFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
    
    for i = 1, 5 do
        pcall(function()
            hrp.CFrame = npcCFrame
            hrp.Velocity = Vector3.new(0, 0, 0)
            hrp.RotVelocity = Vector3.new(0, 0, 0)
        end)
        wait(0.1)
    end
    
    wait(0.5)
    return true
end

local function fastAcceptQuest(questData, npc)
    print("📥 [Quest] กำลังรับเควส...")
    
    -- คลิก NPC แบบเร็ว
    for i = 1, 3 do
        clickNPC(npc)
        wait(0.2)
    end
    
    wait(0.5)
    
    -- ส่งคำสั่งรับเควส
    pcall(function()
        if questData.dialogueSteps then
            for i, step in ipairs(questData.dialogueSteps) do
                local args = {{Choice = step}}
                ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(args))
                wait(0.3)
            end
        end
        local args2 = {{Exit = true}}
        ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(args2))
    end)
    
    wait(0.5)
    print("✅ [Quest] ส่งคำสั่งรับเควสแล้ว!")
end

local function fastSubmitQuest(questData, npc)
    print("📤 [Quest] กำลังส่งเควส...")
    
    -- คลิก NPC แบบเร็ว
    for i = 1, 3 do
        clickNPC(npc)
        wait(0.2)
    end
    
    wait(0.5)
    
    -- ส่งคำสั่งส่งเควส
    pcall(function()
        local args = {{Choice = "Yes, I've completed it."}}
        ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(args))
        wait(0.5)
        
        local exitArgs = {{Exit = true}}
        ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(exitArgs))
    end)
    
    wait(0.5)
    print("✅ [Quest] ส่งคำสั่งส่งเควสแล้ว!")
end

local function talkToNPC(questData)
    pcall(function()
        if questData.dialogueSteps then
            for i, step in ipairs(questData.dialogueSteps) do
                local args = {{Choice = step}}
                ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(args))
                wait(0.5)
            end
        end
        local args2 = {{Exit = true}}
        ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(args2))
    end)
end

local function submitQuest(questData, npc)
    pcall(function()
        clickNPC(npc)
        wait(0.5)
        
        print("📬 [Submit] กำลังส่งเควส...")
        local args = {{Choice = "Yes, I've completed it."}}
        ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(args))
        wait(1)
        
        local exitArgs = {{Exit = true}}
        ReplicatedStorage:WaitForChild("Remote"):WaitForChild("Event"):WaitForChild("Dialogue"):FireServer(unpack(exitArgs))
        wait(0.5)
        
        print("✅ [Submit] ส่งสัญญาณส่งเควสแล้ว!")
    end)
end

local function hasActiveQuest(questData)
    local success, result = pcall(function()
        return player.PlayerGui.Main.QuestAlertFrame.QuestFrame.ScrollingFrame:FindFirstChild(questData.questUIName)
    end)
    return success and result ~= nil
end

local function getQuestMob(mobName)
    local livesFolder = workspace:FindFirstChild(livesFolderName)
    if not livesFolder then return nil end
    local mob = livesFolder:FindFirstChild(mobName)
    if mob and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") and mob.Humanoid.Health > 0 then
        return mob
    end
    return nil
end

local function getSummonLocation(questData)
    local current = workspace
    for _, part in ipairs(questData.summonLocation) do
        current = current:FindFirstChild(part)
        if not current then return nil end
    end
    return current
end

local function pressSummonKey(key)
    pcall(function()
        vu:CaptureController()
        vu:SetKeyDown(string.lower(key))
        task.wait(0.1)
        vu:SetKeyUp(string.lower(key))
    end)
end

local function forceTP(targetCFrame)
    local hrp = getHRP()
    if not hrp or not targetCFrame then 
        return false 
    end
    
    local char = getChar()
    
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
    
    for i = 1, 15 do
        pcall(function()
            hrp.CFrame = targetCFrame
            hrp.Velocity = Vector3.new(0, 0, 0)
            hrp.RotVelocity = Vector3.new(0, 0, 0)
        end)
        wait(0.05)
    end
    
    wait(0.3)
    
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" and part.Name ~= "Head" then
            part.CanCollide = true
        end
    end
    
    return true
end

local function enterDungeonUI(dungeonName)
    combatPaused = true
    wait(0.3)
    
    local dungeonButtonClicked = false
    pcall(function()
        local mainGui = player.PlayerGui:FindFirstChild("Main")
        if mainGui then
            local functionFrame = mainGui:FindFirstChild("FunctionFrame")
            if functionFrame then
                local dungeonBtn = functionFrame:FindFirstChild("Dungeon")
                if dungeonBtn then
                    for _, child in ipairs(dungeonBtn:GetDescendants()) do
                        if (child:IsA("TextButton") or child:IsA("ImageButton")) and child.Visible then
                            for _, connection in pairs(getconnections(child.MouseButton1Click)) do
                                connection:Fire()
                                dungeonButtonClicked = true
                            end
                            if not dungeonButtonClicked then
                                child.MouseButton1Click:Fire()
                                dungeonButtonClicked = true
                            end
                            break
                        end
                    end
                    
                    if not dungeonButtonClicked and (dungeonBtn:IsA("TextButton") or dungeonBtn:IsA("ImageButton")) then
                        for _, connection in pairs(getconnections(dungeonBtn.MouseButton1Click)) do
                            connection:Fire()
                            dungeonButtonClicked = true
                        end
                        if not dungeonButtonClicked then
                            dungeonBtn.MouseButton1Click:Fire()
                            dungeonButtonClicked = true
                        end
                    end
                end
            end
        end
    end)
    
    if dungeonButtonClicked then
        wait(2)
    else
        wait(1)
        return false
    end
    
    local dungeonGUI = nil
    for attempt = 1, 5 do
        dungeonGUI = player.PlayerGui:FindFirstChild("DungeonGUI")
        if dungeonGUI then 
            break 
        end
        wait(1)
    end
    
    if not dungeonGUI then
        return false
    end
    
    local dungeonList = dungeonGUI:FindFirstChild("Base")
    if dungeonList then
        dungeonList = dungeonList:FindFirstChild("DungeonList")
    end
    
    if not dungeonList then
        return false
    end
    
    local targetDungeon = dungeonList:FindFirstChild(dungeonName)
    if not targetDungeon then
        return false
    end
    
    local enterButton = nil
    for _, child in ipairs(targetDungeon:GetDescendants()) do
        if child:IsA("TextButton") and (child.Text == "Enter" or child.Name == "Enter" or string.find(string.lower(child.Text or ""), "enter")) then
            enterButton = child
            break
        end
    end
    
    if not enterButton then
        return false
    end
    
    pcall(function()
        for _, connection in pairs(getconnections(enterButton.MouseButton1Click)) do
            connection:Fire()
        end
        enterButton.MouseButton1Click:Fire()
    end)
    wait(2)
    
    local confirmButton = nil
    local attempts = 0
    while attempts < 10 and not confirmButton do
        for _, gui in ipairs(player.PlayerGui:GetChildren()) do
            for _, child in ipairs(gui:GetDescendants()) do
                if child:IsA("TextButton") and child.Visible and (child.Text == "Confirm" or child.Name == "Confirm" or string.find(string.lower(child.Text or ""), "confirm")) then
                    confirmButton = child
                    break
                end
            end
            if confirmButton then break end
        end
        if not confirmButton then
            attempts = attempts + 1
            wait(0.5)
        end
    end
    
    if not confirmButton then
        return false
    end
    
    pcall(function()
        for _, connection in pairs(getconnections(confirmButton.MouseButton1Click)) do
            connection:Fire()
        end
        confirmButton.MouseButton1Click:Fire()
    end)
    
    for i = 30, 1, -1 do
        if not state.autoQuest or not programRunning then break end
        wait(1)
    end
    
    return true
end

local function summonAndKillStoneBoss(stoneData, bossNames)
    combatPaused = true
    wait(0.3)
    
    local livesFolder = workspace:FindFirstChild(livesFolderName)
    if not livesFolder then
        return false
    end
    
    local targetCFrame = stoneData.cframe * CFrame.new(0, 3, 5)
    for tpCount = 1, 3 do
        forceTP(targetCFrame)
        wait(1)
    end
    
    for pressCount = 1, 8 do
        local hrp = getHRP()
        pcall(function()
            if hrp then
                hrp.CFrame = targetCFrame
                hrp.Velocity = Vector3.new(0, 0, 0)
                hrp.RotVelocity = Vector3.new(0, 0, 0)
            end
        end)
        wait(0.2)
        pressSummonKey("E")
        wait(0.3)
    end
    
    wait(2)
    
    local bossFound = nil
    local searchAttempts = 0
    local maxSearchAttempts = 40
    
    while programRunning and state.autoQuest and searchAttempts < maxSearchAttempts do
        for _, bossName in ipairs(bossNames) do
            local boss = livesFolder:FindFirstChild(bossName)
            if boss and boss:FindFirstChild("HumanoidRootPart") and boss:FindFirstChild("Humanoid") and boss.Humanoid.Health > 0 then
                bossFound = boss
                combatPaused = false
                wait(0.5)
                break
            end
        end
        
        if bossFound then break end
        
        if searchAttempts % 8 == 0 and searchAttempts > 0 then
            local hrp = getHRP()
            pcall(function()
                if hrp then
                    hrp.CFrame = targetCFrame
                    hrp.Velocity = Vector3.new(0, 0, 0)
                end
            end)
            wait(0.2)
            pressSummonKey("E")
        end
        
        searchAttempts = searchAttempts + 1
        wait(1)
    end
    
    if not bossFound then
        combatPaused = true
        return false
    end
    
    local bossHRP = bossFound:FindFirstChild("HumanoidRootPart")
    local bossHumanoid = bossFound:FindFirstChild("Humanoid")
    
    while programRunning and state.autoQuest and bossFound.Parent and bossHumanoid.Health > 0 do
        if bossHRP and bossHRP.Parent then
            local backDistance = 3
            local backPos = bossHRP.Position - bossHRP.CFrame.LookVector * backDistance
            
            pcall(function()
                local hrp = getHRP()
                if hrp then
                    hrp.CFrame = CFrame.new(backPos, Vector3.new(bossHRP.Position.X, backPos.Y, bossHRP.Position.Z))
                end
            end)
        else
            break
        end
        wait(0.15)
    end
    
    combatPaused = true
    wait(2)
    return true
end

-- =================== Auto Level Farm Loop ===================
spawn(function()
    local lastLevel = 0
    local currentTargetMonster = nil
    local lastLevelCheckTime = 0
    local monstersKilled = 0
    local sessionStartTime = os.time()
    
    while programRunning do
        if state.autoLevelFarm then
            local currentTime = tick()
            
            if currentTime - lastLevelCheckTime >= 3 then
                local playerLevel = getPlayerLevel()
                lastLevelCheckTime = currentTime
                
                if levelFarmCurrentLevel then
                    levelFarmCurrentLevel.Text = "📊 เลเวลปัจจุบัน: " .. playerLevel
                end
                
                if playerLevel >= 80 then
                    state.autoLevelFarm = false
                    
                    if levelFarmStatusLabel then
                        levelFarmStatusLabel.Text = "✅ เลเวล 80 - หยุดอัตโนมัติ!"
                        levelFarmStatusLabel.TextColor3 = Color3.fromRGB(67, 181, 129)
                    end
                    if levelFarmCurrentTarget then
                        levelFarmCurrentTarget.Text = "🎯 เป้าหมาย: เสร็จสิ้น!"
                        levelFarmCurrentTarget.TextColor3 = Color3.fromRGB(67, 181, 129)
                    end
                    break
                end
                
                if playerLevel ~= lastLevel or not currentTargetMonster then
                    lastLevel = playerLevel
                    local targetMonster = getTargetMonsterByLevel(playerLevel)
                    
                    if targetMonster and targetMonster ~= "STOP" then
                        if targetMonster ~= currentTargetMonster then
                            currentTargetMonster = targetMonster
                            
                            if levelFarmCurrentTarget then
                                levelFarmCurrentTarget.Text = "🎯 เป้าหมาย: " .. currentTargetMonster
                            end
                        end
                    else
                        wait(5)
                        continue
                    end
                end
            end
            
            if currentTargetMonster then
                local monster = findMonsterByName(currentTargetMonster)
                
                if monster then
                    local mobHRP = monster:FindFirstChild("HumanoidRootPart")
                    local mobHumanoid = monster:FindFirstChild("Humanoid")
                    
                    if mobHRP and mobHumanoid and mobHumanoid.Health > 0 then
                        local hrp = getHRP()
                        if hrp then
                            local distance = (hrp.Position - mobHRP.Position).Magnitude
                            
                            if distance > 50 then
                                local backDistance = 5
                                local targetPos = mobHRP.Position - mobHRP.CFrame.LookVector * backDistance
                                local targetCF = CFrame.new(targetPos, Vector3.new(mobHRP.Position.X, targetPos.Y, mobHRP.Position.Z))
                                
                                smartTeleport(targetCF)
                                wait(0.5)
                            else
                                local backDistance = 3
                                local backPos = mobHRP.Position - mobHRP.CFrame.LookVector * backDistance
                                
                                pcall(function()
                                    hrp.CFrame = CFrame.new(backPos, Vector3.new(mobHRP.Position.X, backPos.Y, mobHRP.Position.Z))
                                end)
                            end
                            
                            local previousHealth = mobHumanoid.Health
                            wait(0.1)
                            if mobHumanoid.Health <= 0 and previousHealth > 0 then
                                monstersKilled = monstersKilled + 1
                                
                                if levelFarmKillCount then
                                    levelFarmKillCount.Text = "💀 ฆ่าแล้ว: " .. monstersKilled .. " ตัว"
                                end
                            end
                        end
                    else
                        wait(0.5)
                    end
                else
                    if levelFarmStatusLabel then
                        levelFarmStatusLabel.Text = "🔍 กำลังค้นหา " .. currentTargetMonster .. "..."
                        levelFarmStatusLabel.TextColor3 = Color3.fromRGB(250, 166, 26)
                    end
                    
                    wait(2)
                end
            end
        else
            wait(1)
        end
        wait(state.autoFarmInterval)
    end
end)

-- =================== Auto Quest Loop (SMART FAST!) ===================
spawn(function()
    while programRunning do
        if state.autoQuest and state.selectedQuest then
            local questData = state.selectedQuest
            local hrp = getHRP()
            if hrp then
                local npc = getNPCFromPath(questData.npcPath)
                if not npc or not npc:FindFirstChild("HumanoidRootPart") then
                    print("❌ [Quest] ไม่พบ NPC!")
                    wait(3)
                    continue
                end
                
                combatPaused = true
                wait(0.2)
                
                print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                print("🚀 [Quest] เริ่มรับเควส: " .. questData.name)
                print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                
                -- 🚀 วาปไป NPC (แค่ครั้งเดียว!)
                if not smartWarpToNPC(questData) then
                    combatPaused = false
                    wait(3)
                    continue
                end
                
                -- รับเควสแบบเร็ว
                fastAcceptQuest(questData, npc)
                
                -- เช็คว่ารับเควสสำเร็จหรือไม่
                local questReceived = false
                for i = 1, 15 do
                    if hasActiveQuest(questData) then
                        questReceived = true
                        print("✅ [Quest] รับเควสสำเร็จ!")
                        break
                    else
                        wait(0.5)
                    end
                end
                
                if not questReceived then
                    print("⚠️ [Quest] รับเควสไม่สำเร็จ ลองใหม่...")
                    combatPaused = false
                    wait(3)
                    continue
                end
                
                wait(0.5)
                
                if questData.questType == "ancient_dungeon" then
                    print("🏛️ [Quest] เข้า Dungeon...")
                    local dungeonEntered = enterDungeonUI(questData.dungeonName)
                    
                    if not dungeonEntered then
                        combatPaused = false
                        wait(5)
                        continue
                    end
                    
                    wait(2)
                    
                    combatPaused = false
                    wait(0.5)
                    
                    local successfulKills = 0
                    for stoneIdx, stoneData in ipairs(questData.stonePositions) do
                        if not state.autoQuest or not programRunning then break end
                        
                        local killed = summonAndKillStoneBoss(stoneData, questData.bossNames)
                        if killed then
                            successfulKills = successfulKills + 1
                        end
                        
                        wait(2)
                    end
                    
                    combatPaused = true
                    
                    local leftDungeon = false
                    pcall(function()
                        for _, gui in ipairs(player.PlayerGui:GetChildren()) do
                            for _, button in ipairs(gui:GetDescendants()) do
                                if button:IsA("TextButton") and button.Visible then
                                    local buttonText = string.lower(button.Text or "")
                                    if string.find(buttonText, "leave") or string.find(buttonText, "exit") or string.find(buttonText, "quit") then
                                        for _, connection in pairs(getconnections(button.MouseButton1Click)) do
                                            connection:Fire()
                                        end
                                        button.MouseButton1Click:Fire()
                                        leftDungeon = true
                                        wait(2)
                                        break
                                    end
                                end
                            end
                            if leftDungeon then break end
                        end
                    end)
                    
                    if leftDungeon then
                        wait(10)
                    else
                        wait(15)
                    end
                    
                    wait(10)
                    
                elseif questData.questType == "summon" then
                    print("👹 [Quest] Summon Boss...")
                    local summonSpot = getSummonLocation(questData)
                    if summonSpot then
                        pcall(function()
                            hrp.CFrame = summonSpot.CFrame * CFrame.new(0, 3, 0)
                        end)
                        wait(1.5)
                        pressSummonKey(questData.summonKey)
                        wait(2)
                        
                        local bossFound = false
                        local attempts = 0
                        local maxAttempts = 100
                        while programRunning and state.autoQuest and not bossFound and attempts < maxAttempts do
                            local boss = getQuestMob(questData.bossName)
                            if boss then
                                bossFound = true
                                
                                combatPaused = false
                                wait(0.5)
                                
                                local bossHRP = boss:FindFirstChild("HumanoidRootPart")
                                local bossHumanoid = boss:FindFirstChild("Humanoid")
                                
                                while programRunning and state.autoQuest and boss.Parent and bossHumanoid and bossHumanoid.Health > 0 do
                                    if bossHRP then
                                        local backDistance = 3
                                        local backPos = bossHRP.Position - bossHRP.CFrame.LookVector * backDistance
                                        pcall(function()
                                            hrp.CFrame = CFrame.new(backPos, Vector3.new(bossHRP.Position.X, backPos.Y, bossHRP.Position.Z))
                                        end)
                                        wait(0.15)
                                    else
                                        break
                                    end
                                end
                                break
                            else
                                attempts = attempts + 1
                                wait(1)
                            end
                        end
                        
                        if not bossFound then
                            combatPaused = false
                            wait(3)
                            continue
                        end
                    else
                        combatPaused = false
                        wait(3)
                        continue
                    end
                    
                elseif questData.questType == "kill" then
                    print("⚔️ [Quest] ฆ่ามอน...")
                    local killedMobs = {}
                    local firstMobFound = false
                    
                    for _, mobName in ipairs(questData.monsters) do
                        local mobFound = false
                        local attempts = 0
                        local maxAttempts = 150
                        
                        while programRunning and state.autoQuest and not killedMobs[mobName] and attempts < maxAttempts do
                            local mob = getQuestMob(mobName)
                            if not mob then
                                if mobFound then
                                    killedMobs[mobName] = true
                                    break
                                else
                                    attempts = attempts + 1
                                    wait(1)
                                end
                            else
                                if not firstMobFound then
                                    combatPaused = false
                                    firstMobFound = true
                                    wait(0.5)
                                end
                                
                                mobFound = true
                                local mobHRP = mob:FindFirstChild("HumanoidRootPart")
                                local mobHumanoid = mob:FindFirstChild("Humanoid")
                                if mobHRP and mobHumanoid and mobHumanoid.Health > 0 then
                                    local backDistance = 3
                                    local backPos = mobHRP.Position - mobHRP.CFrame.LookVector * backDistance
                                    pcall(function()
                                        hrp.CFrame = CFrame.new(backPos, Vector3.new(mobHRP.Position.X, backPos.Y, mobHRP.Position.Z))
                                    end)
                                    wait(0.15)
                                else
                                    killedMobs[mobName] = true
                                    break
                                end
                            end
                            wait(0.1)
                        end
                        if killedMobs[mobName] then
                            wait(1)
                        end
                    end
                end
                
                wait(1)
                
                combatPaused = true
                wait(0.2)
                
                print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                print("📤 [Quest] กลับไปส่งเควส")
                print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                
                -- 🚀 วาปกลับไป NPC (แค่ครั้งเดียว!)
                if not smartWarpToNPC(questData) then
                    combatPaused = false
                    wait(3)
                    continue
                end
                
                -- ส่งเควสแบบเร็ว
                fastSubmitQuest(questData, npc)
                
                -- เช็คว่าส่งเควสสำเร็จหรือไม่
                local questSubmitted = false
                for i = 1, 15 do
                    if not hasActiveQuest(questData) then
                        questSubmitted = true
                        print("✅ [Quest] ส่งเควสสำเร็จ!")
                        break
                    else
                        wait(0.5)
                    end
                end
                
                combatPaused = false
                
                if questSubmitted then
                    print("🎉 [Quest] รอบนี้เสร็จสมบูรณ์!")
                    wait(2)
                else
                    print("⚠️ [Quest] ส่งเควสอาจไม่สำเร็จ ลองใหม่...")
                    wait(2)
                end
            end
        else
            if state.autoQuest and not state.selectedQuest then
                state.autoQuest = false
            end
            if not state.autoQuest and combatPaused then
                combatPaused = false
            end
        end
        wait(1)
    end
end)

-- =================== Auto Dungeon Loop ===================
spawn(function()
    while programRunning do
        if state.autoDungeon then
            combatPaused = true
            
            local hrp = getHRP()
            if not hrp then
                wait(2)
                continue
            end
            
            local selectedBossData = bossDatabase.ethernal
            
            local dungeonEntered = enterDungeonUI(selectedBossData.dungeonName)
            
            if not dungeonEntered then
                wait(5)
                continue
            end
            
            local livesFolder = workspace:FindFirstChild(livesFolderName)
            if not livesFolder then
                wait(5)
                continue
            end
            
            local bossFound = false
            local searchAttempts = 0
            local maxSearchAttempts = 90
            
            while programRunning and state.autoDungeon and searchAttempts < maxSearchAttempts do
                local boss = nil
                
                for _, bossName in ipairs(selectedBossData.bossNames) do
                    boss = livesFolder:FindFirstChild(bossName)
                    if boss and boss:FindFirstChild("HumanoidRootPart") and boss:FindFirstChild("Humanoid") then
                        if boss.Humanoid.Health > 0 then
                            break
                        end
                    end
                    boss = nil
                end
                
                if boss then
                    bossFound = true
                    local bossHRP = boss:FindFirstChild("HumanoidRootPart")
                    local bossHumanoid = boss:FindFirstChild("Humanoid")
                    
                    local bossCFrame = bossHRP.CFrame * CFrame.new(0, 0, 10)
                    forceTP(bossCFrame)
                    wait(1.5)
                    
                    combatPaused = false
                    wait(0.5)
                    
                    while programRunning and state.autoDungeon and boss.Parent and bossHumanoid.Health > 0 do
                        if bossHRP and bossHRP.Parent then
                            local backDistance = 3
                            local backPos = bossHRP.Position - bossHRP.CFrame.LookVector * backDistance
                            
                            pcall(function()
                                hrp = getHRP()
                                if hrp then
                                    hrp.CFrame = CFrame.new(backPos, Vector3.new(bossHRP.Position.X, backPos.Y, bossHRP.Position.Z))
                                end
                            end)
                        else
                            break
                        end
                        wait(0.15)
                    end
                    
                    combatPaused = true
                    
                    wait(20)
                    break
                end
                
                searchAttempts = searchAttempts + 1
                wait(1)
            end
            
            wait(5)
        else
            if combatPaused and not state.autoQuest then
                combatPaused = false
            end
            wait(1)
        end
    end
end)

-- ⚡ Auto Cronus Boss Loop (GUARANTEED TO WORK!)
spawn(function()
    while programRunning do
        if state.autoCronus then
            print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
            print("🛑 [Cronus] เปิดระบบ Auto Cronus")
            print("🛑 [Cronus] Auto Attack/Skills/X จะไม่ทำงานจนกว่าจะเจอบอส!")
            print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
            
            -- อัปเดต UI
            if cronusStatusLabel then
                cronusStatusLabel.Text = "🔍 สถานะ: ค้นหาบอส..."
                cronusStatusLabel.TextColor3 = colors.info
            end
            if cronusCombatLabel then
                cronusCombatLabel.Text = "⚔️ Combat: ❌ STOP!"
                cronusCombatLabel.TextColor3 = colors.danger
            end
            if cronusStatusStroke then
                cronusStatusStroke.Color = colors.info
            end
            
            local hrp = getHRP()
            if hrp then
                local boss = getCronusBoss()
                
                if boss and boss.Parent and boss:FindFirstChild("Humanoid") and boss.Humanoid.Health > 0 then
                    local bossHRP = boss:FindFirstChild("HumanoidRootPart")
                    
                    if bossHRP then
                        print("✅ [Cronus] พบบอสแล้ว! กำลังวาร์ป...")
                        
                        -- อัปเดต UI
                        if cronusStatusLabel then
                            cronusStatusLabel.Text = "✅ พบบอส! กำลังวาร์ป..."
                            cronusStatusLabel.TextColor3 = colors.warning
                        end
                        if cronusCombatLabel then
                            cronusCombatLabel.Text = "⚔️ Combat: 🔄 กำลังวาร์ป..."
                            cronusCombatLabel.TextColor3 = colors.warning
                        end
                        
                        -- วาร์ปไปหาบอส
                        local backDistance = 3
                        local backPos = bossHRP.Position - bossHRP.CFrame.LookVector * backDistance
                        
                        for i = 1, 8 do
                            pcall(function()
                                hrp.CFrame = CFrame.new(backPos, Vector3.new(bossHRP.Position.X, backPos.Y, bossHRP.Position.Z))
                                hrp.Velocity = Vector3.new(0, 0, 0)
                            end)
                            wait(0.15)
                        end
                        
                        print("📍 [Cronus] วาร์ปเสร็จแล้ว! รอ 2 วินาที...")
                        wait(2)
                        
                        print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                        print("⚔️ [Cronus] ปิด Auto Cronus ชั่วคราว!")
                        print("✅ ตอนนี้ Auto Attack/Skills/X จะทำงานได้!")
                        print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                        
                        -- ⚠️ KEY CHANGE: ปิด autoCronus ชั่วคราวเพื่อให้ตีได้!
                        local tempCronusState = true
                        state.autoCronus = false
                        
                        wait(1)
                        
                        -- อัปเดต UI
                        if cronusStatusLabel then
                            cronusStatusLabel.Text = "⚔️ สถานะ: กำลังตีบอส!"
                            cronusStatusLabel.TextColor3 = colors.success
                        end
                        if cronusCombatLabel then
                            cronusCombatLabel.Text = "⚔️ Combat: ✅ กำลังตี!"
                            cronusCombatLabel.TextColor3 = colors.success
                        end
                        if cronusStatusStroke then
                            cronusStatusStroke.Color = colors.success
                        end
                        
                        -- Loop ตีบอส
                        print("⚔️ [Cronus] Loop ตีบอส...")
                        while programRunning and tempCronusState and boss.Parent and boss.Humanoid.Health > 0 do
                            if bossHRP and bossHRP.Parent then
                                local desired = bossHRP.Position - bossHRP.CFrame.LookVector * backDistance
                                pcall(function()
                                    hrp = getHRP()
                                    if hrp then
                                        hrp.CFrame = CFrame.new(desired, Vector3.new(bossHRP.Position.X, desired.Y, bossHRP.Position.Z))
                                    end
                                end)
                            else
                                break
                            end
                            wait(0.1)
                        end
                        
                        print("💀 [Cronus] บอสตาย! เปิด Auto Cronus กลับมา...")
                        
                        -- เปิด autoCronus กลับมา
                        state.autoCronus = tempCronusState
                        
                        -- อัปเดต UI
                        if cronusStatusLabel then
                            cronusStatusLabel.Text = "💀 บอสตาย! รอค้นหาใหม่..."
                            cronusStatusLabel.TextColor3 = colors.warning
                        end
                        if cronusCombatLabel then
                            cronusCombatLabel.Text = "⚔️ Combat: ❌ หยุดตี"
                            cronusCombatLabel.TextColor3 = colors.danger
                        end
                        if cronusStatusStroke then
                            cronusStatusStroke.Color = colors.warning
                        end
                        
                        wait(5)
                    end
                else
                    print("❌ [Cronus] ยังไม่พบบอส...")
                    
                    if cronusStatusLabel then
                        cronusStatusLabel.Text = "❌ ยังไม่พบบอส..."
                        cronusStatusLabel.TextColor3 = colors.danger
                    end
                    if cronusCombatLabel then
                        cronusCombatLabel.Text = "⚔️ Combat: ❌ STOP!"
                        cronusCombatLabel.TextColor3 = colors.danger
                    end
                    
                    wait(3)
                end
            else
                wait(2)
            end
        else
            wait(1)
        end
    end
end)

-- =================== Auto Event Loop ===================
spawn(function()
    while programRunning do
        if state.autoEvent then
            local hrp = getHRP()
            if not hrp then
                wait(2)
                continue
            end
            
            local chest = workspace:FindFirstChild("KeyItem")
            if chest then
                chest = chest:FindFirstChild("Halloween Chest")
            end
            
            if not chest then
                wait(5)
                continue
            end
            
            local chestCFrame = nil
            if chest:IsA("Model") then
                local primary = chest.PrimaryPart or chest:FindFirstChild("HumanoidRootPart") or chest:FindFirstChildWhichIsA("BasePart")
                if primary then
                    chestCFrame = primary.CFrame * CFrame.new(0, 5, 5)
                end
            elseif chest:IsA("BasePart") then
                chestCFrame = chest.CFrame * CFrame.new(0, 5, 5)
            end
            
            if not chestCFrame then
                wait(5)
                continue
            end
            
            forceTP(chestCFrame)
            wait(1.5)
            
            pressSummonKey("E")
            wait(5)
            
            local livesFolder = workspace:FindFirstChild(livesFolderName)
            if livesFolder then
                local function getHallowedGoons()
                    local goons = {}
                    for _, mob in ipairs(livesFolder:GetChildren()) do
                        if mob.Name == "Hollowed Goon Lv.80" and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") then
                            if mob.Humanoid.Health > 0 then
                                table.insert(goons, mob)
                            end
                        end
                    end
                    return goons
                end
                
                local waitTime = 0
                while waitTime < 10 do
                    local currentGoons = getHallowedGoons()
                    if #currentGoons >= 1 then
                        break
                    end
                    wait(1)
                    waitTime = waitTime + 1
                end
                
                local killAttempts = 0
                while programRunning and state.autoEvent and killAttempts < 100 do
                    local goons = getHallowedGoons()
                    
                    if #goons == 0 then
                        break
                    end
                    
                    table.sort(goons, function(a, b)
                        local distA = (hrp.Position - a.HumanoidRootPart.Position).Magnitude
                        local distB = (hrp.Position - b.HumanoidRootPart.Position).Magnitude
                        return distA < distB
                    end)
                    
                    local targetGoon = goons[1]
                    if targetGoon and targetGoon.Parent then
                        local mobHRP = targetGoon:FindFirstChild("HumanoidRootPart")
                        local mobHumanoid = targetGoon:FindFirstChild("Humanoid")
                        
                        if mobHRP and mobHumanoid and mobHumanoid.Health > 0 then
                            while programRunning and state.autoEvent and targetGoon.Parent and mobHumanoid.Health > 0 do
                                local backDistance = 3
                                local desired = mobHRP.Position - mobHRP.CFrame.LookVector * backDistance
                                pcall(function()
                                    hrp.CFrame = CFrame.new(desired, Vector3.new(mobHRP.Position.X, desired.Y, mobHRP.Position.Z))
                                end)
                                wait(0.15)
                            end
                        end
                    end
                    
                    killAttempts = killAttempts + 1
                    wait(0.1)
                end
                
                wait(2)
                forceTP(chestCFrame)
                wait(1.5)
                
                pressSummonKey("E")
                wait(2)
                
                wait(3)
            end
        else
            wait(1)
        end
    end
end)

-- =================== AutoFarm Loop ===================
spawn(function()
    while programRunning do
        if state.autoFarm then
            local char = getChar()
            local event = char:FindFirstChild("PlayerHandler") and char.PlayerHandler:FindFirstChild("HandlerEvent")
            if event then
                local mobs = getNearbyMonsters()
                if #mobs > 0 then
                    local target = mobs[1]
                    local thrp = target:FindFirstChild("HumanoidRootPart")
                    if thrp then
                        local backDistance = 3
                        local backPos = thrp.Position - thrp.CFrame.LookVector * backDistance
                        local hrp = getHRP()
                        if hrp then
                            pcall(function()
                                hrp.CFrame = CFrame.new(backPos, Vector3.new(thrp.Position.X, backPos.Y, thrp.Position.Z))
                            end)
                        end
                        wait(0.05)
                        while programRunning and state.autoFarm and target.Parent and target:FindFirstChild("Humanoid") and target.Humanoid.Health > 0 do
                            local hrp2 = getHRP()
                            local thrp2 = target:FindFirstChild("HumanoidRootPart")
                            if not (hrp2 and thrp2) then break end
                            pcall(function()
                                local desired = thrp2.Position - thrp2.CFrame.LookVector * backDistance
                                hrp2.CFrame = CFrame.new(desired, Vector3.new(thrp2.Position.X, desired.Y, thrp2.Position.Z))
                            end)
                            wait(state.autoFarmInterval)
                        end
                    end
                end
            end
        end
        wait(0.1)
    end
end)

-- =================== Auto Boss Loop (Possessed Rider) ===================
spawn(function()
    while programRunning do
        if state.autoBoss then
            local hrp = getHRP()
            if hrp then
                local rushZone = workspace.MAP:FindFirstChild("Trial") and workspace.MAP.Trial:FindFirstChild("Rush - Zone")
                if rushZone then
                    pcall(function()
                        hrp.CFrame = rushZone.CFrame + Vector3.new(0,5,0)
                    end)
                    wait(0.5)
                end
                local boss = getBoss()
                if boss and boss.Parent and boss:FindFirstChild("Humanoid") and boss.Humanoid.Health > 0 then
                    local bossHRP = boss:FindFirstChild("HumanoidRootPart")
                    if bossHRP then
                        local backDistance = 3
                        local backPos = bossHRP.Position - bossHRP.CFrame.LookVector*backDistance
                        pcall(function()
                            hrp.CFrame = CFrame.new(backPos, Vector3.new(bossHRP.Position.X, backPos.Y, bossHRP.Position.Z))
                        end)
                        wait(0.05)
                        while programRunning and state.autoBoss and boss.Parent and boss.Humanoid.Health > 0 do
                            local desired = bossHRP.Position - bossHRP.CFrame.LookVector*backDistance
                            pcall(function()
                                hrp.CFrame = CFrame.new(desired, Vector3.new(bossHRP.Position.X, desired.Y, bossHRP.Position.Z))
                            end)
                            wait(0.1)
                        end
                    end
                else
                    wait(1)
                end
            end
        end
        wait(0.1)
    end
end)

-- =================== Auto Mine Loop (WARP MODE! วาปตีทีละตัว!) ===================
spawn(function()
    while programRunning do
        if state.autoMine then
            local hrp = getHRP()
            local livesFolder = workspace:FindFirstChild(livesFolderName)
            if hrp and livesFolder then
                -- 1️⃣ หามอน Miner ทั้งหมดที่ยังมีชีวิต
                local miners = {}
                for _, mob in ipairs(livesFolder:GetChildren()) do
                    if mob.Name == "Miner Goon Lv.50" and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") then
                        if mob.Humanoid.Health > 0 then
                            table.insert(miners, mob)
                        end
                    end
                end
                
                -- 2️⃣ เรียงตามระยะใกล้-ไกล
                table.sort(miners, function(a, b)
                    return (hrp.Position - a.HumanoidRootPart.Position).Magnitude < (hrp.Position - b.HumanoidRootPart.Position).Magnitude
                end)
                
                -- 3️⃣ เลือกแค่ 3 ตัวแรก
                local targetMiners = {}
                for i = 1, math.min(3, #miners) do 
                    table.insert(targetMiners, miners[i]) 
                end
                
                -- 4️⃣ วาปไปตีทีละตัว (ไม่ดึงมอนมา!)
                for mobIndex, mob in ipairs(targetMiners) do
                    if not (programRunning and state.autoMine) then break end
                    
                    local mobHRP = mob:FindFirstChild("HumanoidRootPart")
                    local humanoid = mob:FindFirstChild("Humanoid")
                    
                    -- เช็คว่ามอนยังมีชีวิตอยู่ไหม
                    if mobHRP and humanoid and humanoid.Health > 0 and mob.Parent then
                        
                        print("⛏️ [Mine] กำลังวาปไปตีมอนตัวที่ " .. mobIndex .. "/3")
                        
                        -- ✅ ระยะตี (ใกล้มาก!)
                        local attackDistance = 2.5
                        
                        -- 🚀 วาปไปหน้ามอนหลายครั้งเพื่อให้แน่ใจ
                        for warpCount = 1, 5 do
                            if not mobHRP.Parent then break end
                            
                            local targetPos = mobHRP.Position - mobHRP.CFrame.LookVector * attackDistance
                            pcall(function()
                                hrp = getHRP()
                                if hrp then
                                    hrp.CFrame = CFrame.new(targetPos, Vector3.new(mobHRP.Position.X, targetPos.Y, mobHRP.Position.Z))
                                    hrp.Velocity = Vector3.new(0, 0, 0)
                                    hrp.RotVelocity = Vector3.new(0, 0, 0)
                                end
                            end)
                            wait(0.05)
                        end
                        
                        -- รอให้ตัวละครอยู่นิ่ง
                        wait(0.2)
                        
                        print("⚔️ [Mine] เริ่มตีมอนตัวที่ " .. mobIndex)
                        
                        -- ✅ Loop ตีจนกว่ามอนจะตาย
                        while programRunning and state.autoMine and mob.Parent and humanoid.Health > 0 do
                            if mobHRP and mobHRP.Parent then
                                local desired = mobHRP.Position - mobHRP.CFrame.LookVector * attackDistance
                                pcall(function()
                                    hrp = getHRP()
                                    if hrp then
                                        hrp.CFrame = CFrame.new(desired, Vector3.new(mobHRP.Position.X, desired.Y, mobHRP.Position.Z))
                                    end
                                end)
                            else
                                break
                            end
                            wait(0.1)
                        end
                        
                        print("💀 [Mine] มอนตัวที่ " .. mobIndex .. " ตายแล้ว! วาปไปตัวถัดไป...")
                        
                        -- ✅ รอให้มอนตายจริงๆ ก่อนไปตัวถัดไป
                        wait(0.5)
                    else
                        print("⚠️ [Mine] มอนตัวที่ " .. mobIndex .. " ตายไปแล้ว ข้าม!")
                    end
                end
                
                -- รอหน่อยก่อนเริ่มรอบใหม่
                wait(1)
            else
                wait(1)
            end
        else
            wait(0.5)
        end
    end
end)

-- =================== Auto Light Attack (100% FIXED!) ===================
spawn(function()
    while programRunning do
        -- ⚠️ ถ้า Auto Cronus เปิดอยู่ → ห้ามทำงานเลย!
        if state.autoCronus then
            wait(0.1)
        elseif state.autoAttack and not combatPaused then
            local char = getChar()
            local event = char:FindFirstChild("PlayerHandler") and char.PlayerHandler:FindFirstChild("HandlerEvent")
            if event then
                local args = {
                    {
                        CombatAction = true,
                        MouseData = CFrame.new(-1230.8199462890625, 38.287479400634766, -65.46951293945312),
                        Input = "Mouse1",
                        LightAttack = true,
                        Attack = true
                    }
                }
                pcall(function() event:FireServer(unpack(args)) end)
            end
            wait(0.5)
        else
            wait(0.1)
        end
    end
end)

-- =================== Auto Heavy Attack (100% FIXED!) ===================
spawn(function()
    while programRunning do
        -- ⚠️ ถ้า Auto Cronus เปิดอยู่ → ห้ามทำงานเลย!
        if state.autoCronus then
            wait(0.1)
        elseif state.autoHeavyAttack and not combatPaused then
            local char = getChar()
            local event = char:FindFirstChild("PlayerHandler") and char.PlayerHandler:FindFirstChild("HandlerEvent")
            if event then
                local args = {
                    {
                        CombatAction = true,
                        MouseData = CFrame.new(-1230.8199462890625, 38.287479400634766, -65.46951293945312),
                        Input = "Mouse2",
                        HeavyAttack = true,
                        Attack = true
                    }
                }
                pcall(function() event:FireServer(unpack(args)) end)
            end
            wait(0.5)
        else
            wait(0.1)
        end
    end
end)


-- =================== Auto Skills (UNIVERSAL - รองรับทุกมาสไรเดอร์!) ===================
for _, skillKey in ipairs({"E", "R", "C", "V"}) do
    spawn(function()
        while programRunning do
            -- ⚠️ ถ้า Auto Cronus เปิดอยู่ → ห้ามทำงานเลย!
            if state.autoCronus then
                wait(0.1)
            elseif state.autoSkills[skillKey] and not combatPaused then
                local char = getChar()
                local event = char:FindFirstChild("PlayerHandler") and char.PlayerHandler:FindFirstChild("HandlerEvent")
                if event then
                    -- ใช้ Mouse position จริงเพื่อรองรับทุกมาสไรเดอร์
                    local mouse = player:GetMouse()
                    local mousePos = mouse.Hit
                    
                    local args = {
                        {
                            Skill = true,
                            AttackType = "Down",
                            Key = skillKey,
                            MouseData = mousePos
                        }
                    }
                    pcall(function() event:FireServer(unpack(args)) end)
                end
                wait(0.5)
            else
                wait(0.1)
            end
        end
    end)
end

-- =================== Auto Press X (100% FIXED!) ===================
spawn(function()
    while programRunning do
        -- ⚠️ ถ้า Auto Cronus เปิดอยู่ → ห้ามทำงานเลย!
        if state.autoCronus then
            wait(0.1)
        elseif state.autoKeyX and not combatPaused then
            pcall(function()
                local character = getChar()
                if character and character:FindFirstChild("PlayerHandler") then
                    local handlerEvent = character.PlayerHandler:FindFirstChild("HandlerEvent")
                    if handlerEvent then
                        local mouse = player:GetMouse()
                        local mousePos = mouse.Hit
                        
                        local forms = {
                            "Survive Bat",
                            "Survive Cobra", 
                            "Survive Dark Dragon",
                            "Survive Dragon",
                            "Raia Survive"
                        }
                        
                        for _, formName in ipairs(forms) do
                            local args = {
                                {
                                    Skill = true,
                                    FormHenshin = formName,
                                    Key = "X",
                                    MouseData = mousePos
                                }
                            }
                            handlerEvent:FireServer(unpack(args))
                            wait(0.1)
                        end
                        
                        game:GetService("VirtualUser"):SetKeyDown("x")
                        wait(0.1)
                        game:GetService("VirtualUser"):SetKeyUp("x")
                    end
                end
            end)
            wait(0.5)
        else
            wait(0.1)
        end
    end
end)

-- ===================  Auto Heal (NEW!) ===================
spawn(function()
    while programRunning do
        if state.autoHeal then
            local char = getChar()
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            
            if humanoid then
                local healthPercent = (humanoid.Health / humanoid.MaxHealth) * 100
                
                -- ถ้าเลือดต่ำกว่า 70% ให้ฮิล
                if healthPercent < 70 then
                    pcall(function()
                        local handlerEvent = char:FindFirstChild("PlayerHandler") and char.PlayerHandler:FindFirstChild("HandlerEvent")
                        if handlerEvent then
                            local args = {
                                {
                                    Heal = true
                                }
                            }
                            handlerEvent:FireServer(unpack(args))
                        end
                    end)
                    wait(0.3) -- ความเร็วส่งข้อมูล
                else
                    wait(0.5) -- เช็คบ่อยๆ Log
                end
            else
                wait(1)
            end
        else
            wait(0.5)
        end
    end
end)

-- =================== ⚡ Auto Henshin (TRULY FIXED!) ===================
spawn(function()
    local isTransformed = false
    local lastCheck = 0
    local checkInterval = 5
    local transformCooldown = 0
    
    while programRunning do
        if state.autoHenshin then
            local currentTime = tick()
            
            if transformCooldown > 0 and currentTime < transformCooldown then
                wait(1)
            elseif currentTime - lastCheck >= checkInterval then
                lastCheck = currentTime
                
                local char = getChar()
                if char and char:FindFirstChild("PlayerHandler") then
                    local shouldTransform = false
                    
                    pcall(function()
                        local humanoid = char:FindFirstChildOfClass("Humanoid")
                        if humanoid then
                            local baseMaxHealth = 1400  -- ✅ แก้ตรงนี้! (เดิม 100)
                            
                            if humanoid.MaxHealth > baseMaxHealth then
                                isTransformed = true
                                shouldTransform = false
                                print("✅ [Henshin] แปรงร่างแล้ว (MaxHP: " .. humanoid.MaxHealth .. ")")
                            else
                                isTransformed = false
                                shouldTransform = true
                                print("⚠️ [Henshin] ยังไม่แปรงร่าง (MaxHP: " .. humanoid.MaxHealth .. ")")
                            end
                            
                            -- Backup check จาก Model
                            if not isTransformed then
                                for _, obj in pairs(char:GetChildren()) do
                                    if obj:IsA("Model") or obj:IsA("Accessory") then
                                        local name = string.lower(obj.Name)
                                        if string.match(name, "survive") or 
                                           string.match(name, "final") or
                                           string.match(name, "armor") or
                                           string.match(name, "rider") then
                                            isTransformed = true
                                            shouldTransform = false
                                            print("✅ [Henshin] พบ Model: " .. obj.Name)
                                            break
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    
                    if shouldTransform and not isTransformed then
                        pcall(function()
                            local handlerEvent = char.PlayerHandler:FindFirstChild("HandlerEvent")
                            if handlerEvent then
                                print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                                print("⚡ [Henshin] กำลังแปรงร่าง...")
                                print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
                                
                                local args = {{Henshin = true}}
                                handlerEvent:FireServer(unpack(args))
                                
                                isTransformed = true
                                transformCooldown = tick() + 30
                                
                                print("✅ [Henshin] ส่งคำสั่งแล้ว!")
                                wait(3)
                            end
                        end)
                    else
                        if isTransformed and currentTime >= transformCooldown then
                            print("✅ [Henshin] ยืนยัน: แปรงร่างอยู่แล้ว")
                            transformCooldown = tick() + 60
                        end
                    end
                else
                    wait(2)
                end
            else
                wait(1)
            end
        else
            isTransformed = false
            transformCooldown = 0
            wait(1)
        end
    end
end)

-- =================== AFK Mode ===================
spawn(function()
    while programRunning do
        if state.afkEnabled then
            vu:CaptureController()
            vu:ClickButton2(Vector2.new(0,0))
        end
        wait(55)
    end
end)

-- =================== Lock Position & Physics ===================
local function ensureBP()
    local hrp = getHRP()
    if hrp and (not bp or not bp.Parent) then
        bp = Instance.new("BodyPosition")
        bp.MaxForce = Vector3.new(1e5,1e5,1e5)
        bp.P = 1e5
        bp.D = 1e4
        bp.Position = hrp.Position
        bp.Parent = hrp
    end
end
local function destroyBP() if bp then bp:Destroy() bp=nil end end

spawn(function()
    while programRunning do
        local char = getChar()
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = state.lockPos and 0 or 16
        end
        local hrp = getHRP()
        if state.lockPos and hrp then ensureBP() bp.Position = hrp.Position else destroyBP() end
        wait(0.05)
    end
end)

-- =================== 🎨 ULTRA MODERN GUI ===================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AdminUltraModern"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- 🌈 Modern Gradient Color Scheme
local colors = {
    bg = Color3.fromRGB(15, 15, 20),
    panel = Color3.fromRGB(20, 22, 30),
    cardBg = Color3.fromRGB(25, 27, 35),
    accent1 = Color3.fromRGB(138, 43, 226), -- Purple
    accent2 = Color3.fromRGB(75, 0, 130), -- Indigo
    success = Color3.fromRGB(16, 185, 129), -- Emerald
    danger = Color3.fromRGB(239, 68, 68), -- Red
    warning = Color3.fromRGB(245, 158, 11), -- Amber
    info = Color3.fromRGB(59, 130, 246), -- Blue
    text = Color3.fromRGB(248, 250, 252),
    textDim = Color3.fromRGB(148, 163, 184),
    hover = Color3.fromRGB(30, 32, 40)
}

-- 🎭 Main Container with Glassmorphism Effect
local mainFrame = Instance.new("Frame")
mainFrame.Name = "Main"
-- 📐 Responsive Size
if isMobile then
    mainFrame.Size = UDim2.new(0.9, 0, 0.85, 0)
    mainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
else
    mainFrame.Size = UDim2.new(0, 520, 0, 600)
    mainFrame.Position = UDim2.new(0.5, -260, 0.5, -300)
end

mainFrame.BackgroundColor3 = colors.bg
mainFrame.BackgroundTransparency = 0.02
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = screenGui
-- 🔎 Auto UI Scale (Mobile Friendly)
local uiScale = Instance.new("UIScale")
uiScale.Parent = mainFrame

if isMobile then
    uiScale.Scale = 0.8   -- ปรับได้ 0.75 – 0.85
else
    uiScale.Scale = 1
end

local mainCorner = Instance.new("UICorner", mainFrame)
mainCorner.CornerRadius = UDim.new(0, 20)

-- ✨ Gradient Border Effect
local mainStroke = Instance.new("UIStroke", mainFrame)
mainStroke.Color = colors.accent1
mainStroke.Thickness = 3
mainStroke.Transparency = 0.3

-- 🎨 Animated Gradient Background
local gradientBG = Instance.new("Frame", mainFrame)
gradientBG.Size = UDim2.new(1, 0, 1, 0)
gradientBG.BackgroundColor3 = colors.panel
gradientBG.BackgroundTransparency = 0.95
gradientBG.BorderSizePixel = 0
gradientBG.ZIndex = 0

local gradientBGCorner = Instance.new("UICorner", gradientBG)
gradientBGCorner.CornerRadius = UDim.new(0, 20)

local gradientUI = Instance.new("UIGradient", gradientBG)
gradientUI.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, colors.accent1),
    ColorSequenceKeypoint.new(0.5, colors.accent2),
    ColorSequenceKeypoint.new(1, colors.accent1)
}
gradientUI.Rotation = 45

-- ⚡ Animated Gradient Effect
spawn(function()
    while programRunning and screenGui.Parent do
        for i = 0, 360, 2 do
            if not programRunning or not screenGui.Parent then break end
            gradientUI.Rotation = i
            wait(0.03)
        end
    end
end)

-- 🎯 Modern Top Bar with Gradient
local topBar = Instance.new("Frame", mainFrame)
topBar.Size = UDim2.new(1, 0, 0, 60)
topBar.BackgroundColor3 = colors.panel
topBar.BackgroundTransparency = 0.3
topBar.BorderSizePixel = 0
topBar.ZIndex = 2

local topBarCorner = Instance.new("UICorner", topBar)
topBarCorner.CornerRadius = UDim.new(0, 20)

local topBarGradient = Instance.new("UIGradient", topBar)
topBarGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, colors.accent1),
    ColorSequenceKeypoint.new(1, colors.accent2)
}
topBarGradient.Rotation = 90
topBarGradient.Transparency = NumberSequence.new{
    NumberSequenceKeypoint.new(0, 0.7),
    NumberSequenceKeypoint.new(1, 0.9)
}

-- 💫 App Icon & Title
local iconFrame = Instance.new("Frame", topBar)
iconFrame.Size = UDim2.new(0, 45, 0, 45)
iconFrame.Position = UDim2.new(0, 15, 0.5, -22.5)
iconFrame.BackgroundColor3 = colors.accent1
iconFrame.BorderSizePixel = 0

local iconCorner = Instance.new("UICorner", iconFrame)
iconCorner.CornerRadius = UDim.new(0, 12)

local iconGradient = Instance.new("UIGradient", iconFrame)
iconGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, colors.accent1),
    ColorSequenceKeypoint.new(1, colors.accent2)
}
iconGradient.Rotation = 135

local iconLabel = Instance.new("TextLabel", iconFrame)
iconLabel.Size = UDim2.new(1, 0, 1, 0)
iconLabel.BackgroundTransparency = 1
iconLabel.Text = "⚡"
iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
iconLabel.Font = Enum.Font.GothamBold
iconLabel.TextSize = 26

local title = Instance.new("TextLabel", topBar)
title.Size = UDim2.new(0, 300, 1, 0)
title.Position = UDim2.new(0, 70, 0, 0)
title.BackgroundTransparency = 1
title.Text = "ULTRA ADMIN"
title.TextColor3 = colors.text
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextXAlignment = Enum.TextXAlignment.Left

local subtitle = Instance.new("TextLabel", topBar)
subtitle.Size = UDim2.new(0, 300, 0, 18)
subtitle.Position = UDim2.new(0, 70, 0, 32)
subtitle.BackgroundTransparency = 1
subtitle.Text = "v5.1 • Cronus Edition"
subtitle.TextColor3 = colors.textDim
subtitle.Font = Enum.Font.Gotham
subtitle.TextSize = 11
subtitle.TextXAlignment = Enum.TextXAlignment.Left

-- 🎮 Control Buttons
local function createTopButton(icon, color, position)
    local btn = Instance.new("TextButton", topBar)
    btn.Size = UDim2.new(0, 38, 0, 38)
    btn.Position = UDim2.new(1, position, 0.5, -19)
    btn.Text = icon
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 18
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    
    local btnCorner = Instance.new("UICorner", btn)
    btnCorner.CornerRadius = UDim.new(0, 10)
    
    local btnGradient = Instance.new("UIGradient", btn)
    btnGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, color),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(
            math.max(0, color.R * 255 - 30)/255,
            math.max(0, color.G * 255 - 30)/255,
            math.max(0, color.B * 255 - 30)/255
        ))
    }
    btnGradient.Rotation = 135
    
    btn.MouseEnter:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.2}):Play()
    end)
    
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0}):Play()
    end)
    
    return btn
end

local btnMin = createTopButton("━", colors.info, -90)
local btnClose = createTopButton("✕", colors.danger, -45)

local dragging = false
local dragStart
local startPos

-- 🖱️📱 Draggable Main UI (TopBar)
topBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (
        input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch
    ) then
        local delta = input.Position - dragStart
        mainFrame.Position = startPos + UDim2.fromOffset(delta.X, delta.Y)
    end
end)


-- 📑 Modern Tab Container
local tabContainer = Instance.new("ScrollingFrame", mainFrame)
tabContainer.Size = UDim2.new(1, -20, 0, 70)
tabContainer.Position = UDim2.new(0, 10, 0, 70)
tabContainer.BackgroundTransparency = 1
tabContainer.BorderSizePixel = 0
tabContainer.ScrollBarThickness = 0
tabContainer.CanvasSize = UDim2.new(0, 650, 0, 70)
tabContainer.ScrollingDirection = Enum.ScrollingDirection.X

local tabLayout = Instance.new("UIListLayout", tabContainer)
tabLayout.FillDirection = Enum.FillDirection.Horizontal
tabLayout.Padding = UDim.new(0, 8)
tabLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left

-- 📄 Content Container
local contentContainer = Instance.new("Frame", mainFrame)
contentContainer.Size = UDim2.new(1, -20, 1, -160)
contentContainer.Position = UDim2.new(0, 10, 0, 150)
contentContainer.BackgroundColor3 = colors.cardBg
contentContainer.BackgroundTransparency = 0.3
contentContainer.BorderSizePixel = 0

local contentCorner = Instance.new("UICorner", contentContainer)
contentCorner.CornerRadius = UDim.new(0, 16)

local contentStroke = Instance.new("UIStroke", contentContainer)
contentStroke.Color = colors.accent1
contentStroke.Transparency = 0.8
contentStroke.Thickness = 1

-- 🎨 Create Modern Tab Function
local function createTab(icon, text, order)
    local btn = Instance.new("TextButton", tabContainer)
    btn.Size = UDim2.new(0, 95, 0, 62)
    btn.Text = ""
    btn.BackgroundColor3 = colors.cardBg
    btn.BackgroundTransparency = 0.5
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.LayoutOrder = order
    
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 12)
    
    local stroke = Instance.new("UIStroke", btn)
    stroke.Color = colors.accent1
    stroke.Transparency = 0.8
    stroke.Thickness = 1.5
    
    local iconLabel = Instance.new("TextLabel", btn)
    iconLabel.Size = UDim2.new(1, 0, 0, 30)
    iconLabel.Position = UDim2.new(0, 0, 0, 8)
    iconLabel.BackgroundTransparency = 1
    iconLabel.Text = icon
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.TextSize = 20
    iconLabel.TextColor3 = colors.textDim
    
    local textLabel = Instance.new("TextLabel", btn)
    textLabel.Size = UDim2.new(1, 0, 0, 18)
    textLabel.Position = UDim2.new(0, 0, 1, -24)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = text
    textLabel.Font = Enum.Font.GothamBold
    textLabel.TextSize = 10
    textLabel.TextColor3 = colors.textDim
    
    btn.MouseEnter:Connect(function()
        if btn.BackgroundTransparency ~= 0 then
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.3}):Play()
            TweenService:Create(stroke, TweenInfo.new(0.2), {Transparency = 0.5}):Play()
        end
    end)
    
    btn.MouseLeave:Connect(function()
        if btn.BackgroundTransparency ~= 0 then
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.5}):Play()
            TweenService:Create(stroke, TweenInfo.new(0.2), {Transparency = 0.8}):Play()
        end
    end)
    
    return btn, iconLabel, textLabel, stroke
end

local tabFarm, tabFarmIcon, tabFarmText, tabFarmStroke = createTab("🎯", "Farm", 1)
local tabBoss, tabBossIcon, tabBossText, tabBossStroke = createTab("👹", "Boss", 2)
local tabMine, tabMineIcon, tabMineText, tabMineStroke = createTab("⛏️", "Mine", 3)
local tabQuest, tabQuestIcon, tabQuestText, tabQuestStroke = createTab("📜", "เควส", 4)
local tabEvent, tabEventIcon, tabEventText, tabEventStroke = createTab("🎃", "Event", 5)
local tabOther, tabOtherIcon, tabOtherText, tabOtherStroke = createTab("⚙️", "Other", 6)

-- 📋 Create Content Scrolls
local function createContent()
    local scroll = Instance.new("ScrollingFrame", contentContainer)
    scroll.Size = UDim2.new(1, -20, 1, -20)
    scroll.Position = UDim2.new(0, 10, 0, 10)
    scroll.BackgroundTransparency = 1
    scroll.BorderSizePixel = 0
    scroll.ScrollBarThickness = 5
    scroll.ScrollBarImageColor3 = colors.accent1
    scroll.Visible = false
    
    local layout = Instance.new("UIListLayout", scroll)
    layout.Padding = UDim.new(0, 12)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    
    return scroll, layout
end

local farmContent, farmLayout = createContent()
local bossContent, bossLayout = createContent()
local mineContent, mineLayout = createContent()
local questContent, questLayout = createContent()
local eventContent, eventLayout = createContent()
local otherContent, otherLayout = createContent()

-- 🎨 Modern Toggle Switch
local function createToggle(parent, icon, text, callback)
    local container = Instance.new("Frame", parent)
    container.Size = UDim2.new(1, 0, 0, 60)
    container.BackgroundColor3 = colors.cardBg
    container.BorderSizePixel = 0
    
    local corner = Instance.new("UICorner", container)
    corner.CornerRadius = UDim.new(0, 12)
    
    local stroke = Instance.new("UIStroke", container)
    stroke.Color = colors.accent1
    stroke.Transparency = 0.85
    stroke.Thickness = 1
    
    -- Icon
    local iconLabel = Instance.new("TextLabel", container)
    iconLabel.Size = UDim2.new(0, 40, 0, 40)
    iconLabel.Position = UDim2.new(0, 10, 0.5, -20)
    iconLabel.BackgroundColor3 = colors.panel
    iconLabel.Text = icon
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.TextSize = 20
    iconLabel.TextColor3 = colors.accent1
    
    local iconCorner = Instance.new("UICorner", iconLabel)
    iconCorner.CornerRadius = UDim.new(0, 10)
    
    -- Text
    local label = Instance.new("TextLabel", container)
    label.Size = UDim2.new(1, -140, 1, 0)
    label.Position = UDim2.new(0, 60, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = colors.text
    label.Font = Enum.Font.GothamBold
    label.TextSize = 13
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextWrapped = true
    
    -- Toggle Switch
    local toggleBG = Instance.new("TextButton", container)
    toggleBG.Size = UDim2.new(0, 60, 0, 32)
    toggleBG.Position = UDim2.new(1, -70, 0.5, -16)
    toggleBG.Text = ""
    toggleBG.BackgroundColor3 = colors.panel
    toggleBG.BorderSizePixel = 0
    toggleBG.AutoButtonColor = false
    
    local toggleBGCorner = Instance.new("UICorner", toggleBG)
    toggleBGCorner.CornerRadius = UDim.new(1, 0)
    
    local toggleIndicator = Instance.new("Frame", toggleBG)
    toggleIndicator.Size = UDim2.new(0, 26, 0, 26)
    toggleIndicator.Position = UDim2.new(0, 3, 0.5, -13)
    toggleIndicator.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    toggleIndicator.BorderSizePixel = 0
    
    local indicatorCorner = Instance.new("UICorner", toggleIndicator)
    indicatorCorner.CornerRadius = UDim.new(1, 0)
    
    local indicatorGradient = Instance.new("UIGradient", toggleIndicator)
    indicatorGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(200, 200, 200)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255))
    }
    
    local isOn = false
    toggleBG.MouseButton1Click:Connect(function()
        isOn = not isOn
        
        local bgColor = isOn and colors.success or colors.panel
        local position = isOn and UDim2.new(1, -29, 0.5, -13) or UDim2.new(0, 3, 0.5, -13)
        
        TweenService:Create(toggleBG, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            BackgroundColor3 = bgColor
        }):Play()
        
        TweenService:Create(toggleIndicator, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Position = position
        }):Play()
        
        TweenService:Create(stroke, TweenInfo.new(0.3), {
            Transparency = isOn and 0.5 or 0.85,
            Color = isOn and colors.success or colors.accent1
        }):Play()
        
        callback(isOn)
    end)
    
    container.MouseEnter:Connect(function()
        TweenService:Create(container, TweenInfo.new(0.2), {BackgroundColor3 = colors.hover}):Play()
    end)
    
    container.MouseLeave:Connect(function()
        TweenService:Create(container, TweenInfo.new(0.2), {BackgroundColor3 = colors.cardBg}):Play()
    end)
    
    return container
end

-- =================== Farm Tab Content ===================
farmContent.Visible = true

-- Level Farm Info Card
local levelFarmCard = Instance.new("Frame", farmContent)
levelFarmCard.Size = UDim2.new(1, 0, 0, 150)
levelFarmCard.BackgroundColor3 = colors.cardBg
levelFarmCard.BorderSizePixel = 0

local levelFarmCorner = Instance.new("UICorner", levelFarmCard)
levelFarmCorner.CornerRadius = UDim.new(0, 12)

local levelFarmStroke = Instance.new("UIStroke", levelFarmCard)
levelFarmStroke.Color = colors.success
levelFarmStroke.Thickness = 2
levelFarmStroke.Transparency = 0.5

local levelFarmGradient = Instance.new("UIGradient", levelFarmCard)
levelFarmGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, colors.cardBg),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(20, 30, 25))
}
levelFarmGradient.Rotation = 45

local levelIcon = Instance.new("TextLabel", levelFarmCard)
levelIcon.Size = UDim2.new(0, 90, 1, -20)
levelIcon.Position = UDim2.new(0, 10, 0, 10)
levelIcon.BackgroundColor3 = colors.panel
levelIcon.Text = "🚀"
levelIcon.Font = Enum.Font.GothamBold
levelIcon.TextSize = 50

local levelIconCorner = Instance.new("UICorner", levelIcon)
levelIconCorner.CornerRadius = UDim.new(0, 10)

local levelInfoFrame = Instance.new("Frame", levelFarmCard)
levelInfoFrame.Size = UDim2.new(1, -120, 1, -20)
levelInfoFrame.Position = UDim2.new(0, 110, 0, 10)
levelInfoFrame.BackgroundTransparency = 1

local levelTitle = Instance.new("TextLabel", levelInfoFrame)
levelTitle.Size = UDim2.new(1, 0, 0, 26)
levelTitle.BackgroundTransparency = 1
levelTitle.Text = "🎯 Smart Level Farm"
levelTitle.TextColor3 = colors.success
levelTitle.Font = Enum.Font.GothamBold
levelTitle.TextSize = 16
levelTitle.TextXAlignment = Enum.TextXAlignment.Left

local levelDesc = Instance.new("TextLabel", levelInfoFrame)
levelDesc.Size = UDim2.new(1, 0, 0, 32)
levelDesc.Position = UDim2.new(0, 0, 0, 28)
levelDesc.BackgroundTransparency = 1
levelDesc.Text = "ระบบฟามอัตโนมัติตามเลเวล\nวาร์ปไปหามอนทุกระยะ หยุดที่ Lv.80"
levelDesc.TextColor3 = colors.textDim
levelDesc.Font = Enum.Font.Gotham
levelDesc.TextSize = 10
levelDesc.TextWrapped = true
levelDesc.TextXAlignment = Enum.TextXAlignment.Left
levelDesc.TextYAlignment = Enum.TextYAlignment.Top

levelFarmCurrentLevel = Instance.new("TextLabel", levelInfoFrame)
levelFarmCurrentLevel.Size = UDim2.new(1, 0, 0, 20)
levelFarmCurrentLevel.Position = UDim2.new(0, 0, 0, 65)
levelFarmCurrentLevel.BackgroundTransparency = 1
levelFarmCurrentLevel.Text = "📊 เลเวล: โหลด..."
levelFarmCurrentLevel.TextColor3 = colors.info
levelFarmCurrentLevel.Font = Enum.Font.GothamBold
levelFarmCurrentLevel.TextSize = 11
levelFarmCurrentLevel.TextXAlignment = Enum.TextXAlignment.Left

levelFarmCurrentTarget = Instance.new("TextLabel", levelInfoFrame)
levelFarmCurrentTarget.Size = UDim2.new(1, 0, 0, 20)
levelFarmCurrentTarget.Position = UDim2.new(0, 0, 0, 88)
levelFarmCurrentTarget.BackgroundTransparency = 1
levelFarmCurrentTarget.Text = "🎯 เป้าหมาย: ยังไม่เริ่ม"
levelFarmCurrentTarget.TextColor3 = colors.textDim
levelFarmCurrentTarget.Font = Enum.Font.GothamBold
levelFarmCurrentTarget.TextSize = 11
levelFarmCurrentTarget.TextXAlignment = Enum.TextXAlignment.Left

levelFarmKillCount = Instance.new("TextLabel", levelInfoFrame)
levelFarmKillCount.Size = UDim2.new(1, 0, 0, 20)
levelFarmKillCount.Position = UDim2.new(0, 0, 0, 111)
levelFarmKillCount.BackgroundTransparency = 1
levelFarmKillCount.Text = "💀 ฆ่าแล้ว: 0 ตัว"
levelFarmKillCount.TextColor3 = colors.warning
levelFarmKillCount.Font = Enum.Font.GothamBold
levelFarmKillCount.TextSize = 11
levelFarmKillCount.TextXAlignment = Enum.TextXAlignment.Left

levelFarmStatusLabel = Instance.new("TextLabel", levelInfoFrame)
levelFarmStatusLabel.Size = UDim2.new(0, 0, 0, 0)
levelFarmStatusLabel.BackgroundTransparency = 1

createToggle(farmContent, "🚀", "Smart Level Farm", function(on)
    state.autoLevelFarm = on
    if on then
        state.autoFarm = false
        local currentLevel = getPlayerLevel()
        levelFarmCurrentLevel.Text = "📊 เลเวล: " .. currentLevel
        local targetMonster = getTargetMonsterByLevel(currentLevel)
        if targetMonster and targetMonster ~= "STOP" then
            levelFarmCurrentTarget.Text = "🎯 เป้าหมาย: " .. targetMonster
        end
    else
        levelFarmCurrentTarget.Text = "🎯 เป้าหมาย: หยุดทำงาน"
    end
end)

-- Separator
local separator1 = Instance.new("Frame", farmContent)
separator1.Size = UDim2.new(1, 0, 0, 1)
separator1.BackgroundColor3 = colors.accent1
separator1.BackgroundTransparency = 0.85
separator1.BorderSizePixel = 0

local farmTitle = Instance.new("TextLabel", farmContent)
farmTitle.Size = UDim2.new(1, 0, 0, 35)
farmTitle.BackgroundTransparency = 1
farmTitle.Text = "📋 Manual Farm"
farmTitle.TextColor3 = colors.accent1
farmTitle.Font = Enum.Font.GothamBold
farmTitle.TextSize = 15
farmTitle.TextXAlignment = Enum.TextXAlignment.Left
farmTitle.TextYAlignment = Enum.TextYAlignment.Center

local farmTitlePadding = Instance.new("UIPadding", farmTitle)
farmTitlePadding.PaddingLeft = UDim.new(0, 10)

createToggle(farmContent, "🎯", "Manual Farm (เลือกมอนเอง)", function(on)
    state.autoFarm = on
    if on then
        state.autoLevelFarm = false
        levelFarmCurrentTarget.Text = "🎯 เป้าหมาย: Manual Mode"
    end
end)

-- Quick Select Buttons
local quickFrame = Instance.new("Frame", farmContent)
quickFrame.Size = UDim2.new(1, 0, 0, 45)
quickFrame.BackgroundTransparency = 1

local quickLayout = Instance.new("UIListLayout", quickFrame)
quickLayout.FillDirection = Enum.FillDirection.Horizontal
quickLayout.Padding = UDim.new(0, 10)
quickLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

local function createQuickBtn(text, icon, color)
    local btn = Instance.new("TextButton", quickFrame)
    btn.Size = UDim2.new(0.48, 0, 1, 0)
    btn.Text = icon .. " " .. text
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 10)
    
    local gradient = Instance.new("UIGradient", btn)
    gradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, color),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(
            math.max(0, color.R * 255 - 20)/255,
            math.max(0, color.G * 255 - 20)/255,
            math.max(0, color.B * 255 - 20)/255
        ))
    }
    gradient.Rotation = 90
    
    btn.MouseEnter:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.2}):Play()
    end)
    
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0}):Play()
    end)
    
    return btn
end

local btnAll = createQuickBtn("เลือกทั้งหมด", "✓", colors.success)
local btnNone = createQuickBtn("ยกเลิกทั้งหมด", "✗", colors.danger)

-- Monster Selection List
local monsterScroll = Instance.new("ScrollingFrame", farmContent)
monsterScroll.Size = UDim2.new(1, 0, 0, 200)
monsterScroll.BackgroundColor3 = colors.cardBg
monsterScroll.BorderSizePixel = 0
monsterScroll.ScrollBarThickness = 5
monsterScroll.ScrollBarImageColor3 = colors.accent1

local monsterScrollCorner = Instance.new("UICorner", monsterScroll)
monsterScrollCorner.CornerRadius = UDim.new(0, 12)

local monsterScrollStroke = Instance.new("UIStroke", monsterScroll)
monsterScrollStroke.Color = colors.accent1
monsterScrollStroke.Transparency = 0.85
monsterScrollStroke.Thickness = 1

local monsterLayout = Instance.new("UIListLayout", monsterScroll)
monsterLayout.Padding = UDim.new(0, 6)
monsterLayout.SortOrder = Enum.SortOrder.LayoutOrder

local monsterButtons = {}
for i, mName in ipairs(targetMonsters) do
    local btn = Instance.new("TextButton", monsterScroll)
    btn.Size = UDim2.new(1, -12, 0, 40)
    btn.Text = "✓ " .. mName
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    btn.BackgroundColor3 = colors.success
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.AutoButtonColor = false
    
    local padding = Instance.new("UIPadding", btn)
    padding.PaddingLeft = UDim.new(0, 12)
    
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 8)
    
    local gradient = Instance.new("UIGradient", btn)
    gradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, colors.success),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 140, 100))
    }
    gradient.Rotation = 90
    
    btn.MouseButton1Click:Connect(function()
        selectedMonsters[mName] = not selectedMonsters[mName]
        btn.Text = (selectedMonsters[mName] and "✓ " or "✗ ") .. mName
        
        local targetColor = selectedMonsters[mName] and colors.success or colors.textDim
        TweenService:Create(btn, TweenInfo.new(0.3), {BackgroundColor3 = targetColor}):Play()
        
        gradient.Color = selectedMonsters[mName] and ColorSequence.new{
            ColorSequenceKeypoint.new(0, colors.success),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 140, 100))
        } or ColorSequence.new(colors.textDim, colors.textDim)
    end)
    
    table.insert(monsterButtons, btn)
end

monsterScroll.CanvasSize = UDim2.new(0, 0, 0, #targetMonsters * 46)

btnAll.MouseButton1Click:Connect(function()
    for i, mName in ipairs(targetMonsters) do
        selectedMonsters[mName] = true
        monsterButtons[i].Text = "✓ " .. mName
        TweenService:Create(monsterButtons[i], TweenInfo.new(0.3), {BackgroundColor3 = colors.success}):Play()
    end
end)

btnNone.MouseButton1Click:Connect(function()
    for i, mName in ipairs(targetMonsters) do
        selectedMonsters[mName] = false
        monsterButtons[i].Text = "✗ " .. mName
        TweenService:Create(monsterButtons[i], TweenInfo.new(0.3), {BackgroundColor3 = colors.textDim}):Play()
    end
end)

farmLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    farmContent.CanvasSize = UDim2.new(0, 0, 0, farmLayout.AbsoluteContentSize.Y + 20)
end)

-- =================== Boss Tab ===================
-- Cronus Status Card
local cronusStatusCard = Instance.new("Frame", bossContent)
cronusStatusCard.Size = UDim2.new(1, 0, 0, 100)
cronusStatusCard.BackgroundColor3 = colors.cardBg
cronusStatusCard.BorderSizePixel = 0
cronusStatusCard.LayoutOrder = 0

local cronusStatusCorner = Instance.new("UICorner", cronusStatusCard)
cronusStatusCorner.CornerRadius = UDim.new(0, 12)

local cronusStatusStroke = Instance.new("UIStroke", cronusStatusCard)
cronusStatusStroke.Color = colors.warning
cronusStatusStroke.Thickness = 2
cronusStatusStroke.Transparency = 0.5

local cronusIcon = Instance.new("TextLabel", cronusStatusCard)
cronusIcon.Size = UDim2.new(0, 70, 1, -20)
cronusIcon.Position = UDim2.new(0, 10, 0, 10)
cronusIcon.BackgroundColor3 = colors.panel
cronusIcon.Text = "⏰"
cronusIcon.Font = Enum.Font.GothamBold
cronusIcon.TextSize = 40

local cronusIconCorner = Instance.new("UICorner", cronusIcon)
cronusIconCorner.CornerRadius = UDim.new(0, 10)

local cronusInfoFrame = Instance.new("Frame", cronusStatusCard)
cronusInfoFrame.Size = UDim2.new(1, -100, 1, -20)
cronusInfoFrame.Position = UDim2.new(0, 90, 0, 10)
cronusInfoFrame.BackgroundTransparency = 1

local cronusTitle = Instance.new("TextLabel", cronusInfoFrame)
cronusTitle.Size = UDim2.new(1, 0, 0, 24)
cronusTitle.BackgroundTransparency = 1
cronusTitle.Text = "⏰ Cronus Boss Status"
cronusTitle.TextColor3 = colors.warning
cronusTitle.Font = Enum.Font.GothamBold
cronusTitle.TextSize = 15
cronusTitle.TextXAlignment = Enum.TextXAlignment.Left

local cronusStatusLabel = Instance.new("TextLabel", cronusInfoFrame)
cronusStatusLabel.Size = UDim2.new(1, 0, 0, 22)
cronusStatusLabel.Position = UDim2.new(0, 0, 0, 28)
cronusStatusLabel.BackgroundTransparency = 1
cronusStatusLabel.Text = "⚪ สถานะ: ปิดอยู่"
cronusStatusLabel.TextColor3 = colors.textDim
cronusStatusLabel.Font = Enum.Font.GothamBold
cronusStatusLabel.TextSize = 12
cronusStatusLabel.TextXAlignment = Enum.TextXAlignment.Left

local cronusCombatLabel = Instance.new("TextLabel", cronusInfoFrame)
cronusCombatLabel.Size = UDim2.new(1, 0, 0, 22)
cronusCombatLabel.Position = UDim2.new(0, 0, 0, 52)
cronusCombatLabel.BackgroundTransparency = 1
cronusCombatLabel.Text = "⚔️ Combat: ปกติ"
cronusCombatLabel.TextColor3 = colors.success
cronusCombatLabel.Font = Enum.Font.Gotham
cronusCombatLabel.TextSize = 11
cronusCombatLabel.TextXAlignment = Enum.TextXAlignment.Left

createToggle(bossContent, "👹", "Farm Possessed Rider Lv.90", function(on) state.autoBoss = on end)
createToggle(bossContent, "⏰", "Farm Cronus Lv.90 (NEW!)", function(on) 
    state.autoCronus = on 
    if on then
        -- 🛑 หยุดการตีทันทีเมื่อเปิด Auto Cronus (FORCE STOP!)
        combatPaused = true
        
        -- รอเพื่อให้แน่ใจว่า combatPaused ถูกเซ็ตก่อน
        wait(0.5)
        
        print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
        print("🛑 [Cronus] เปิดระบบ Auto Cronus")
        print("🛑 [Cronus] หยุดการตีทันที! combatPaused = " .. tostring(combatPaused))
        print("🔍 [Cronus] รอค้นหาบอส Cronus Lv.90...")
        print("━━━━━━━━━━━━━━━━━━━━━━━━━━━━")
        
        -- อัปเดต UI
        if cronusStatusLabel then
            cronusStatusLabel.Text = "🔍 สถานะ: กำลังค้นหาบอส..."
            cronusStatusLabel.TextColor3 = colors.info
        end
        if cronusCombatLabel then
            cronusCombatLabel.Text = "⚔️ Combat: ❌ หยุดตี (รอบอส)"
            cronusCombatLabel.TextColor3 = colors.danger
        end
        if cronusStatusStroke then
            cronusStatusStroke.Color = colors.info
        end
    else
        -- คืนค่า combat เมื่อปิด (ถ้าไม่มี Quest/Dungeon ทำงาน)
        if not state.autoQuest and not state.autoDungeon then
            combatPaused = false
            print("✅ [Cronus] ปิดระบบ Auto Cronus - คืนค่า combat ให้ปกติ")
        end
        
        -- อัปเดต UI
        cronusStatusLabel.Text = "⚪ สถานะ: ปิดอยู่"
        cronusStatusLabel.TextColor3 = colors.textDim
        cronusCombatLabel.Text = "⚔️ Combat: ปกติ"
        cronusCombatLabel.TextColor3 = colors.success
        cronusStatusStroke.Color = colors.warning
    end
end)
createToggle(bossContent, "🏛️", "Auto Dungeon (Trial of Ethernal)", function(on) state.autoDungeon = on end)

bossLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    bossContent.CanvasSize = UDim2.new(0, 0, 0, bossLayout.AbsoluteContentSize.Y + 20)
end)

-- =================== Mine Tab ===================
createToggle(mineContent, "⛏️", "Auto Mine (Miner Goon Lv.50)", function(on) state.autoMine = on end)

mineLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    mineContent.CanvasSize = UDim2.new(0, 0, 0, mineLayout.AbsoluteContentSize.Y + 20)
end)

local questTitle = Instance.new("TextLabel", questContent)
questTitle.Size = UDim2.new(1, 0, 0, 50)
questTitle.BackgroundTransparency = 1
questTitle.Text = "🚀 Remote Quest System\n(ไม่ต้องวาป!)"
questTitle.TextColor3 = colors.success
questTitle.Font = Enum.Font.GothamBold
questTitle.TextSize = 16
questTitle.TextXAlignment = Enum.TextXAlignment.Center
questTitle.TextYAlignment = Enum.TextYAlignment.Center

local questButtons = {}
for idx, questData in ipairs(questDatabase) do
    local questCard = Instance.new("TextButton", questContent)
    questCard.Size = UDim2.new(1, 0, 0, 90)
    questCard.BackgroundColor3 = colors.cardBg
    questCard.BorderSizePixel = 0
    questCard.Text = ""
    questCard.AutoButtonColor = false
    
    local cardCorner = Instance.new("UICorner", questCard)
    cardCorner.CornerRadius = UDim.new(0, 12)
    
    local cardStroke = Instance.new("UIStroke", questCard)
    cardStroke.Color = colors.accent1
    cardStroke.Thickness = 1.5
    cardStroke.Transparency = 0.85
    
    local iconFrame = Instance.new("Frame", questCard)
    iconFrame.Size = UDim2.new(0, 70, 1, -16)
    iconFrame.Position = UDim2.new(0, 8, 0, 8)
    iconFrame.BackgroundColor3 = colors.panel
    iconFrame.BorderSizePixel = 0
    
    local iconCorner = Instance.new("UICorner", iconFrame)
    iconCorner.CornerRadius = UDim.new(0, 10)
    
    local iconLabel = Instance.new("TextLabel", iconFrame)
    iconLabel.Size = UDim2.new(1, 0, 1, 0)
    iconLabel.BackgroundTransparency = 1
    iconLabel.Text = questData.icon
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.TextSize = 35
    
    local infoFrame = Instance.new("Frame", questCard)
    infoFrame.Size = UDim2.new(1, -90, 1, -16)
    infoFrame.Position = UDim2.new(0, 86, 0, 8)
    infoFrame.BackgroundTransparency = 1
    
    local questNameLabel = Instance.new("TextLabel", infoFrame)
    questNameLabel.Size = UDim2.new(1, 0, 0, 22)
    questNameLabel.BackgroundTransparency = 1
    questNameLabel.Text = questData.name
    questNameLabel.TextColor3 = colors.text
    questNameLabel.Font = Enum.Font.GothamBold
    questNameLabel.TextSize = 14
    questNameLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local npcLabel = Instance.new("TextLabel", infoFrame)
    npcLabel.Size = UDim2.new(1, 0, 0, 18)
    npcLabel.Position = UDim2.new(0, 0, 0, 24)
    npcLabel.BackgroundTransparency = 1
    npcLabel.Text = "👤 NPC: " .. questData.npcName
    npcLabel.TextColor3 = colors.textDim
    npcLabel.Font = Enum.Font.Gotham
    npcLabel.TextSize = 11
    npcLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local typeLabel = Instance.new("TextLabel", infoFrame)
    typeLabel.Size = UDim2.new(1, 0, 0, 30)
    typeLabel.Position = UDim2.new(0, 0, 0, 44)
    typeLabel.BackgroundTransparency = 1
    if questData.questType == "kill" then
        typeLabel.Text = "⚔️ ฆ่ามอน: " .. #questData.monsters .. " ตัว"
    elseif questData.questType == "summon" then
        typeLabel.Text = "👹 Summon Boss"
    elseif questData.questType == "ancient_dungeon" then
        typeLabel.Text = "🏛️ Dungeon Quest"
    end
    typeLabel.TextColor3 = colors.textDim
    typeLabel.Font = Enum.Font.Gotham
    typeLabel.TextSize = 11
    typeLabel.TextXAlignment = Enum.TextXAlignment.Left
    typeLabel.TextWrapped = true
    
    questCard.MouseButton1Click:Connect(function()
        state.selectedQuest = questData
        for _, cardData in ipairs(questButtons) do
            TweenService:Create(cardData.card, TweenInfo.new(0.3), {BackgroundColor3 = colors.cardBg}):Play()
            TweenService:Create(cardData.stroke, TweenInfo.new(0.3), {Transparency = 0.85, Color = colors.accent1}):Play()
        end
        TweenService:Create(questCard, TweenInfo.new(0.3), {BackgroundColor3 = colors.hover}):Play()
        TweenService:Create(cardStroke, TweenInfo.new(0.3), {Transparency = 0.3, Color = colors.success}):Play()
    end)
    
    questCard.MouseEnter:Connect(function()
        if state.selectedQuest ~= questData then
            TweenService:Create(questCard, TweenInfo.new(0.2), {BackgroundColor3 = colors.hover}):Play()
        end
    end)
    
    questCard.MouseLeave:Connect(function()
        if state.selectedQuest ~= questData then
            TweenService:Create(questCard, TweenInfo.new(0.2), {BackgroundColor3 = colors.cardBg}):Play()
        end
    end)
    
    table.insert(questButtons, {card = questCard, stroke = cardStroke})
end

createToggle(questContent, "🚀", "เปิดระบบเควสอัตโนมัติ", function(on)
    if not state.selectedQuest then
        return
    end
    state.autoQuest = on
end)

questLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    questContent.CanvasSize = UDim2.new(0, 0, 0, questLayout.AbsoluteContentSize.Y + 20)
end)

-- =================== Event Tab ===================
local eventTitle = Instance.new("TextLabel", eventContent)
eventTitle.Size = UDim2.new(1, 0, 0, 40)
eventTitle.BackgroundTransparency = 1
eventTitle.Text = "🎃 Halloween Event"
eventTitle.TextColor3 = colors.warning
eventTitle.Font = Enum.Font.GothamBold
eventTitle.TextSize = 16
eventTitle.TextXAlignment = Enum.TextXAlignment.Center

createToggle(eventContent, "🎃", "Auto Halloween Event", function(on) state.autoEvent = on end)

eventLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    eventContent.CanvasSize = UDim2.new(0, 0, 0, eventLayout.AbsoluteContentSize.Y + 20)
end)

-- =================== Other Tab ===================
local otherTitle = Instance.new("TextLabel", otherContent)
otherTitle.Size = UDim2.new(1, 0, 0, 40)
otherTitle.BackgroundTransparency = 1
otherTitle.Text = "⚙️ การตั้งค่า"
otherTitle.TextColor3 = colors.accent1
otherTitle.Font = Enum.Font.GothamBold
otherTitle.TextSize = 16
otherTitle.TextXAlignment = Enum.TextXAlignment.Center

createToggle(otherContent, "🔒", "Lock Position", function(on) state.lockPos = on end)
createToggle(otherContent, "😴", "AFK Mode", function(on) state.afkEnabled = on end)
createToggle(otherContent, "💚", "Auto Heal (เลือด < 70%)", function(on) state.autoHeal = on end)
createToggle(otherContent, "⚡", "Auto Henshin (แปรงร่างอัตโนมัติ)", function(on) state.autoHenshin = on end)
createToggle(otherContent, "⚔️", "Auto Light Attack", function(on) state.autoAttack = on end)
createToggle(otherContent, "💥", "Auto Heavy Attack", function(on) state.autoHeavyAttack = on end)
createToggle(otherContent, "❌", "Auto Press X", function(on) state.autoKeyX = on end)
createToggle(otherContent, "🔥", "Auto Skill E", function(on) state.autoSkills.E = on end)
createToggle(otherContent, "💫", "Auto Skill R", function(on) state.autoSkills.R = on end)
createToggle(otherContent, "⚡", "Auto Skill C", function(on) state.autoSkills.C = on end)
createToggle(otherContent, "✨", "Auto Skill V", function(on) state.autoSkills.V = on end)



otherLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    otherContent.CanvasSize = UDim2.new(0, 0, 0, otherLayout.AbsoluteContentSize.Y + 20)
end)

-- =================== Tab Switching with Animation ===================
local function switchTab(tabName)
    local tabs = {
        {name = "Farm", content = farmContent, btn = tabFarm, icon = tabFarmIcon, text = tabFarmText, stroke = tabFarmStroke},
        {name = "Boss", content = bossContent, btn = tabBoss, icon = tabBossIcon, text = tabBossText, stroke = tabBossStroke},
        {name = "Mine", content = mineContent, btn = tabMine, icon = tabMineIcon, text = tabMineText, stroke = tabMineStroke},
        {name = "Quest", content = questContent, btn = tabQuest, icon = tabQuestIcon, text = tabQuestText, stroke = tabQuestStroke},
        {name = "Event", content = eventContent, btn = tabEvent, icon = tabEventIcon, text = tabEventText, stroke = tabEventStroke},
        {name = "Other", content = otherContent, btn = tabOther, icon = tabOtherIcon, text = tabOtherText, stroke = tabOtherStroke}
    }
    
    for _, tab in ipairs(tabs) do
        local isActive = tab.name == tabName
        tab.content.Visible = isActive
        
        TweenService:Create(tab.btn, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            BackgroundTransparency = isActive and 0 or 0.5
        }):Play()
        
        TweenService:Create(tab.stroke, TweenInfo.new(0.3), {
            Transparency = isActive and 0.3 or 0.8
        }):Play()
        
        TweenService:Create(tab.icon, TweenInfo.new(0.3), {
            TextColor3 = isActive and colors.text or colors.textDim
        }):Play()
        
        TweenService:Create(tab.text, TweenInfo.new(0.3), {
            TextColor3 = isActive and colors.text or colors.textDim
        }):Play()
    end
end

tabFarm.MouseButton1Click:Connect(function() switchTab("Farm") end)
tabBoss.MouseButton1Click:Connect(function() switchTab("Boss") end)
tabMine.MouseButton1Click:Connect(function() switchTab("Mine") end)
tabQuest.MouseButton1Click:Connect(function() switchTab("Quest") end)
tabEvent.MouseButton1Click:Connect(function() switchTab("Event") end)
tabOther.MouseButton1Click:Connect(function() switchTab("Other") end)

-- =================== Minimize Button ===================
local minimized = false
local rBtn = Instance.new("TextButton")

rBtn.Position = UDim2.new(0, 50, 0, 100)
rBtn.BackgroundColor3 = colors.accent1
rBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
rBtn.Text = "⚡"
rBtn.Font = Enum.Font.GothamBold
if isMobile then
    rBtn.Size = UDim2.new(0, 56, 0, 56)
    rBtn.TextSize = 26
else
    rBtn.Size = UDim2.new(0, 70, 0, 70)
    rBtn.TextSize = 32
end

rBtn.Visible = false
rBtn.BorderSizePixel = 0
rBtn.Parent = screenGui

local rCorner = Instance.new("UICorner", rBtn)
rCorner.CornerRadius = UDim.new(1, 0)

local rGradient = Instance.new("UIGradient", rBtn)
rGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, colors.accent1),
    ColorSequenceKeypoint.new(1, colors.accent2)
}
rGradient.Rotation = 135

local rStroke = Instance.new("UIStroke", rBtn)
rStroke.Color = Color3.fromRGB(255, 255, 255)
rStroke.Thickness = 3
rStroke.Transparency = 0.5

btnMin.MouseButton1Click:Connect(function()
    minimized = true
    TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0),
        Position = UDim2.new(0.5, 0, 0.5, 0)
    }):Play()
    wait(0.3)
    mainFrame.Visible = false
    rBtn.Visible = true
    TweenService:Create(rBtn, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, 70, 0, 70)
    }):Play()
end)

rBtn.MouseButton1Click:Connect(function()
    minimized = false
    TweenService:Create(rBtn, TweenInfo.new(0.2, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0)
    }):Play()
    wait(0.2)
    rBtn.Visible = false
    mainFrame.Visible = true
    mainFrame.Size = UDim2.new(0, 0, 0, 0)
    mainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
  if isMobile then
    mainFrame.Size = UDim2.new(0.9, 0, 0.85, 0)
    mainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
else
    mainFrame.Size = UDim2.new(0, 520, 0, 600)
    mainFrame.Position = UDim2.new(0.5, -260, 0.5, -300)
end

end)

-- Draggable R Button
local rDragging = false
local rDragStart, rStartPos

rBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
or input.UserInputType == Enum.UserInputType.Touch then

        rDragging = true
        rDragStart = input.Position
        rStartPos = rBtn.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                rDragging = false
            end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if rDragging and (
    input.UserInputType == Enum.UserInputType.MouseMovement
    or input.UserInputType == Enum.UserInputType.Touch
) then

        local delta = input.Position - rDragStart
        local newX = rStartPos.X.Offset + delta.X
        local newY = rStartPos.Y.Offset + delta.Y
        local vs = workspace.CurrentCamera.ViewportSize
        newX = math.clamp(newX, 0, vs.X - rBtn.AbsoluteSize.X)
        newY = math.clamp(newY, 0, vs.Y - rBtn.AbsoluteSize.Y)
        rBtn.Position = UDim2.new(0, newX, 0, newY)
    end
end)

-- =================== Close Confirmation ===================
btnClose.MouseButton1Click:Connect(function()
    TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0),
        Position = UDim2.new(0.5, 0, 0.5, 0)
    }):Play()
    wait(0.3)
    programRunning = false
    screenGui:Destroy()
end)

print("🎨 Ultra Modern Admin UI v5.1 loaded successfully!")
print("✨ Created by: Modern UI Designer")
print("🌈 Features: Gradient colors, smooth animations, glassmorphism")
print("⚡ NEW: Cronus Lv.90 Boss Farm added!")
