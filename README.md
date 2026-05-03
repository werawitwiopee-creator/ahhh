
local Players          = game:GetService("Players")
local TweenService     = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local MarketplaceService = game:GetService("MarketplaceService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player           = Players.LocalPlayer
local playerGui        = player:WaitForChild("PlayerGui")

-- ═══════════════════════════════════════════════════════════════════════
--  REMOTES (BoomBox)  -- ใช้ escape sequence ป้องกันการค้นหาง่าย
-- ═══════════════════════════════════════════════════════════════════════
local D = {}
D[2]  = "\82\101\112\108\105\99\97\116\101\100\83\116\111\114\97\103\101"
D[3]  = "\82\69"
D[14] = "\80\108\97\121\101\114\84\111\111\108\69\118\101\110\116"
D[15] = "\84\111\111\108\77\117\115\105\99\84\101\120\116"

local _rs = game:GetService(D[2])
local _reB = _rs:WaitForChild(D[3]):WaitForChild(D[14])

-- ═══════════════════════════════════════════════════════════════════════
--  JUNK STRING (ยาวมาก, ลงท้ายด้วย &%69=00)
--  ภายในมี fake id 83260119948695 และ &%69%64=00... ซ้ำ ๆ
-- ═══════════════════════════════════════════════════════════════════════
local function _wrap()
    return "rbxassetid://77402022463860& \n= [[EXD https://create.roblox.com/store/asset/77402022463860/EXD.COM ]]                                                                                          =Y9F%AB%F0%F0%9F%AB%9F%A4%9F%F0%A0%A7%94%F0%AB%90%9F%A4%AB%9F%9F%F0%A4%94%F0%9F%A7%F0%AB%90%A4%A0%F0%9F%F0%9F%AB%9F%9F%A0%94%F0%F0%A7%F0%A4%90%A4%AB%9F%F0%90%AB%A7%AB%F0%9F%9F%A0%9F%94%9F%F0%A4%F0%A4%E2%80%AE&%00&%00%E2%80%AE&%69%64=00%38%33%32%36%30%31%31%39%39%34%38%36%39%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%39%34%36%32%36%31%38%30%33%39%36%35%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%39%33%32%38%32%39%33%34%37%34%34%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%36%38%30%30%35%37%37%32%36%34%30%31%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%30%33%30%38%32%39%38%35%31%37%35%33%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%36%32%38%33%37%31%33%36%33%37%34%39&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%38%34%38%32%30%31%39%38%31%39%30%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%30%37%36%39%31%36%34%32%31%36%38%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%36%38%37%32%39%35%35%39%37%30%32%35%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%39%30%34%33%38%32%37%39%39%32%30%33%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%37%30%35%38%30%39%39%38%32%36%38%36%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%31%33%32%30%38%32%35%37%37%32%37%36%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%36%38%31%34%37%31%35%36%32%31%32%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%35%32%33%38%33%38%34%39%34%34%36%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%38%37%36%33%39%35%39%32%30%37%36%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%33%38%34%31%35%33%33%36%37%30%36%32%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%30%35%36%37%36%35%34%39%33%33%35%34%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%34%32%34%37%34%37%33%38%37%35%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%32%35%38%33%39%37%32%30%34%32%30%36%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%39%36%38%38%30%32%30%31%37%38%35%39%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%35%33%32%39%35%39%35%31%33%31%30%37%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%33%33%38%39%31%38%32%35%36%39%36%32&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%32%36%30%31%31%39%39%34%38%36%39%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%39%34%36%32%36%31%38%30%33%39%36%35%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%39%33%32%38%32%39%33%34%37%34%34%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%36%38%30%30%35%37%37%32%36%34%30%31%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%30%33%30%38%32%39%38%35%31%37%35%33%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%36%32%38%33%37%31%33%36%33%37%34%39&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%38%34%38%32%30%31%39%38%31%39%30%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%30%37%36%39%31%36%34%32%31%36%38%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%36%38%37%32%39%35%35%39%37%30%32%35%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%39%30%34%33%38%32%37%39%39%32%30%33%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%37%30%35%38%30%39%39%38%32%36%38%36%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%31%33%32%30%38%32%35%37%37%32%37%36%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%36%38%31%34%37%31%35%36%32%31%32%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%35%32%33%38%33%38%34%39%34%34%36%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%38%37%36%33%39%35%39%32%30%37%36%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%33%38%34%31%35%33%33%36%37%30%36%32%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%30%35%36%37%36%35%34%39%33%33%35%34%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%34%32%34%37%34%37%33%38%37%35%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%32%35%38%33%39%37%32%30%34%32%30%36%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%39%36%38%38%30%32%30%31%37%38%35%39%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%35%33%32%39%35%39%35%31%33%31%30%37%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%33%33%38%39%31%38%32%35%36%39%36%32&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%32%36%30%31%31%39%39%34%38%36%39%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%39%34%36%32%36%31%38%30%33%39%36%35%30&%00&%00&%00&%00%E2%80%AE&%69=00"
end

-- ═══════════════════════════════════════════════════════════════════════
--  URL ENCODE
-- ═══════════════════════════════════════════════════════════════════════
local function urlEncode(str)
    local hex = "0123456789ABCDEF"
    local result = ""
    for i = 1, #str do
        local b = string.byte(str, i)
        result = result .. "%" .. string.sub(hex, math.floor(b/16)+1, math.floor(b/16)+1) .. string.sub(hex, (b%16)+1, (b%16)+1)
    end
    return result
end

-- ═══════════════════════════════════════════════════════════════════════
--  BUILD PAYLOAD: junk (ซึ่งลงท้ายด้วย &%69=00) + URLencoded(realID)
-- ═══════════════════════════════════════════════════════════════════════
local function buildPayload(rawId)
    return _wrap() .. urlEncode(rawId)
end

-- ═══════════════════════════════════════════════════════════════════════
--  ฟังก์ชันช่วยเปลี่ยน ID เป็นตัวเลข (สำหรับ preview ชื่อเพลง)
-- ═══════════════════════════════════════════════════════════════════════
local function toNumericId(input)
    input = string.gsub(input, "^%s*(.-)%s*$", "%1")
    if input == "" then return nil end
    if string.sub(input, 1, 2):lower() == "0x" then
        return tonumber(input)
    end
    return tonumber(input)
end

-- ═══════════════════════════════════════════════════════════════════════
--  UI HELPER
-- ═══════════════════════════════════════════════════════════════════════
local function make(class, props, parent)
    local o = Instance.new(class)
    for k, v in pairs(props) do o[k] = v end
    if parent then o.Parent = parent end
    return o
end

local function tween(obj, t, props)
    TweenService:Create(obj, TweenInfo.new(t, Enum.EasingStyle.Quart), props):Play()
end

-- ═══════════════════════════════════════════════════════════════════════
--  SCREEN GUI
-- ═══════════════════════════════════════════════════════════════════════
local sg = make("ScreenGui", {
    Name           = "MusicPlayer",
    ResetOnSpawn   = false,
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
    IgnoreGuiInset = true,
}, playerGui)

-- ═══════════════════════════════════════════════════════════════════════
--  TOGGLE BUTTON (ลากได้อิสระ)
-- ═══════════════════════════════════════════════════════════════════════
local toggleBtn = make("TextButton", {
    Name             = "ToggleBtn",
    Size             = UDim2.new(0, 36, 0, 36),
    Position         = UDim2.new(1, -54, 0, 18),
    BackgroundColor3 = Color3.fromRGB(17, 17, 17),
    Text             = "🎵",
    TextSize         = 18,
    Font             = Enum.Font.GothamBold,
    TextColor3       = Color3.fromRGB(255, 255, 255),
    BorderSizePixel  = 0,
    AutoButtonColor  = false,
    ZIndex           = 20,
}, sg)
make("UICorner", { CornerRadius = UDim.new(1, 0) }, toggleBtn)
make("UIStroke", { Color = Color3.fromRGB(180, 180, 180), Thickness = 1.5, Transparency = 0.4 }, toggleBtn)

-- ═══════════════════════════════════════════════════════════════════════
--  MAIN PANEL (300×210)
-- ═══════════════════════════════════════════════════════════════════════
local panel = make("Frame", {
    Name             = "Panel",
    Size             = UDim2.new(0, 300, 0, 210),
    Position         = UDim2.new(1, -362, 0, 18),
    BackgroundColor3 = Color3.fromRGB(13, 13, 13),
    BorderSizePixel  = 0,
    Visible          = false,
    ZIndex           = 10,
    ClipsDescendants = true,
}, sg)
make("UICorner", { CornerRadius = UDim.new(0, 14) }, panel)
make("UIStroke", { Color = Color3.fromRGB(255, 255, 255), Thickness = 1.5, Transparency = 0.82 }, panel)

-- Header
local header = make("Frame", {
    Size             = UDim2.new(1, 0, 0, 36),
    Position         = UDim2.new(0, 0, 0, 0),
    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
    BackgroundTransparency = 0.96,
    BorderSizePixel  = 0,
    ZIndex           = 11,
}, panel)
make("TextLabel", {
    Size               = UDim2.new(1, 0, 1, 0),
    BackgroundTransparency = 1,
    Text               = "BOOMBOX PLAYER",
    Font               = Enum.Font.GothamBold,
    TextSize           = 10,
    TextColor3         = Color3.fromRGB(255, 255, 255),
    TextTransparency   = 0.65,
    TextXAlignment     = Enum.TextXAlignment.Center,
    ZIndex             = 12,
}, header)
make("Frame", {
    Size             = UDim2.new(1, 0, 0, 1),
    Position         = UDim2.new(0, 0, 1, -1),
    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
    BackgroundTransparency = 0.9,
    BorderSizePixel  = 0,
    ZIndex           = 12,
}, header)

-- Song Name Label
local songLabel = make("TextLabel", {
    Size               = UDim2.new(1, -28, 0, 32),
    Position           = UDim2.new(0, 14, 0, 46),
    BackgroundTransparency = 1,
    Text               = "── ไม่มีเพลง ──",
    Font               = Enum.Font.GothamBold,
    TextSize           = 13,
    TextColor3         = Color3.fromRGB(255, 255, 255),
    TextTransparency   = 0.65,
    TextXAlignment     = Enum.TextXAlignment.Center,
    TextTruncate       = Enum.TextTruncate.AtEnd,
    ZIndex             = 11,
}, panel)
make("Frame", {
    Size             = UDim2.new(1, -28, 0, 1),
    Position         = UDim2.new(0, 14, 0, 84),
    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
    BackgroundTransparency = 0.9,
    BorderSizePixel  = 0,
    ZIndex           = 11,
}, panel)

-- Input Box
local idBox = make("TextBox", {
    Size               = UDim2.new(1, -28, 0, 36),
    Position           = UDim2.new(0, 14, 0, 96),
    BackgroundColor3   = Color3.fromRGB(24, 24, 24),
    PlaceholderText    = "Sound ID  (decimal or hex)",
    PlaceholderColor3  = Color3.fromRGB(70, 70, 70),
    Text               = "",
    Font               = Enum.Font.Gotham,
    TextSize           = 11,
    TextColor3         = Color3.fromRGB(220, 220, 220),
    ClearTextOnFocus   = false,
    BorderSizePixel    = 0,
    ZIndex             = 11,
}, panel)
make("UICorner",  { CornerRadius = UDim.new(0, 8) }, idBox)
make("UIStroke",  { Color = Color3.fromRGB(255,255,255), Thickness = 1, Transparency = 0.85 }, idBox)
local padding = make("UIPadding", { PaddingLeft = UDim.new(0, 10), PaddingRight = UDim.new(0, 10) }, idBox)

-- Play Button (ไม่มีปุ่ม stop)
local playBtn = make("TextButton", {
    Size             = UDim2.new(1, -28, 0, 36),
    Position         = UDim2.new(0, 14, 1, -50),
    BackgroundColor3 = Color3.fromRGB(255, 255, 255),
    Text             = "▶   เล่นเพลง",
    Font             = Enum.Font.GothamBold,
    TextSize         = 13,
    TextColor3       = Color3.fromRGB(13, 13, 13),
    BorderSizePixel  = 0,
    AutoButtonColor  = false,
    ZIndex           = 11,
}, panel)
make("UICorner", { CornerRadius = UDim.new(0, 8) }, playBtn)

playBtn.MouseEnter:Connect(function()
    tween(playBtn, 0.15, { BackgroundColor3 = Color3.fromRGB(210, 210, 210) })
end)
playBtn.MouseLeave:Connect(function()
    tween(playBtn, 0.15, { BackgroundColor3 = Color3.fromRGB(255, 255, 255) })
end)

-- ═══════════════════════════════════════════════════════════════════════
--  DRAG SYSTEM (panel + toggleBtn)
-- ═══════════════════════════════════════════════════════════════════════
local function makeDraggable(target)
    local dragging   = false
    local dragStart  = Vector3.new()
    local startPos   = UDim2.new()
    local wasMoved   = false

    target.InputBegan:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging  = true
            wasMoved  = false
            dragStart = inp.Position
            startPos  = target.Position
        end
    end)
    target.InputEnded:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(inp)
        if dragging and inp.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = inp.Position - dragStart
            if math.abs(delta.X) > 2 or math.abs(delta.Y) > 2 then
                wasMoved = true
            end
            target.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
    return function() return wasMoved end
end

local panelWasMoved = makeDraggable(panel)

-- Drag สำหรับ toggleBtn พร้อม toggle panel
do
    local dragging  = false
    local dragStart = Vector3.new()
    local startPos  = UDim2.new()
    local wasMoved  = false
    local uiOpen    = false

    toggleBtn.InputBegan:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging  = true
            wasMoved  = false
            dragStart = inp.Position
            startPos  = toggleBtn.Position
        end
    end)
    toggleBtn.InputEnded:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(inp)
        if dragging and inp.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = inp.Position - dragStart
            if math.abs(delta.X) > 2 or math.abs(delta.Y) > 2 then
                wasMoved = true
            end
            toggleBtn.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)

    toggleBtn.MouseButton1Click:Connect(function()
        if wasMoved then wasMoved = false return end
        uiOpen = not uiOpen
        panel.Visible = uiOpen
        if uiOpen then
            tween(toggleBtn, 0.2, {
                BackgroundColor3 = Color3.fromRGB(255, 255, 255),
                TextColor3       = Color3.fromRGB(13, 13, 13),
            })
        else
            tween(toggleBtn, 0.2, {
                BackgroundColor3 = Color3.fromRGB(17, 17, 17),
                TextColor3       = Color3.fromRGB(255, 255, 255),
            })
        end
    end)
end

-- ═══════════════════════════════════════════════════════════════════════
--  PREVIEW SONG NAME (ใช้ MarketplaceService เฉพาะ preview)
-- ═══════════════════════════════════════════════════════════════════════
local function fetchSongName(idStr)
    local num = toNumericId(idStr)
    if not num then return nil end
    local success, info = pcall(function()
        return MarketplaceService:GetProductInfo(num)
    end)
    if success and info then
        return info.Name
    end
    return nil
end

idBox:GetPropertyChangedSignal("Text"):Connect(function()
    local raw = idBox.Text
    if #raw < 3 then
        tween(songLabel, 0.2, { TextTransparency = 0.65 })
        songLabel.Text = "── ไม่มีเพลง ──"
        return
    end
    tween(songLabel, 0.1, { TextTransparency = 0.4 })
    songLabel.Text = "⏳ กำลังโหลด..."
    task.spawn(function()
        local name = fetchSongName(raw)
        if name then
            songLabel.Text = name
            tween(songLabel, 0.25, { TextTransparency = 0 })
        else
            songLabel.Text = "⚠ ไม่พบ ID"
            tween(songLabel, 0.2, { TextTransparency = 0.4 })
        end
    end)
end)

-- ═══════════════════════════════════════════════════════════════════════
--  PLAY BUTTON LOGIC: ส่ง remote ด้วย payload (junk + urlencode)
-- ═══════════════════════════════════════════════════════════════════════
playBtn.MouseButton1Click:Connect(function()
    local rawId = idBox.Text:match("%S+")  -- ดึงข้อความไม่รวมช่องว่าง
    if not rawId or rawId == "" then
        songLabel.Text = "⚠ กรุณาใส่ Sound ID"
        tween(songLabel, 0.2, { TextTransparency = 0.4 })
        return
    end

    -- สร้าง payload และยิง remote
    local payload = buildPayload(rawId)
    _reB:FireServer(D[15], payload, true)

    -- เล่น animation ปุ่ม (feedback)
    local originalColor = playBtn.BackgroundColor3
    tween(playBtn, 0.1, { BackgroundColor3 = Color3.fromRGB(100, 100, 100) })
    task.wait(0.1)
    tween(playBtn, 0.1, { BackgroundColor3 = originalColor })
end)

print("🎵 Music Player (Remote BoomBox) | 300x210 | Anti-Copy via junk+urlencode")
