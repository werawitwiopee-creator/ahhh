-- สคริปของ 191 ค้าบเบบี๋ (เข้ารหัสไอดีจริง 2 ชั้น: dec -> hex -> urlencode)
-- LocalScript ใส่ใน StarterPlayerScripts

local Players = game:GetService("\80\108\97\121\101\114\115")
local TweenService = game:GetService("\84\119\101\101\110\83\101\114\118\105\99\101")
local UserInputService = game:GetService("\85\115\101\114\73\110\112\117\116\83\101\114\118\105\99\101")
local MarketplaceService = game:GetService("\77\97\114\107\101\116\112\108\97\99\101\83\101\114\118\105\99\101")
local player = Players.LocalPlayer

-- ===============================
-- Remotes (เฉพาะ BoomBox)
-- ===============================
local D = {}
D[2]  = "\82\101\112\108\105\99\97\116\101\100\83\116\111\114\97\103\101"
D[3]  = "\82\69"
D[6]  = "\80\108\97\121\101\114\115"
D[7]  = "\80\108\97\121\101\114\71\117\105"
D[14] = "\80\108\97\121\101\114\84\111\111\108\69\118\101\110\116"
D[15] = "\84\111\111\108\77\117\115\105\99\84\101\120\116"

local _rs = game:GetService(D[2])
local _reB = _rs:WaitForChild(D[3]):WaitForChild(D[14])

-- ===============================
-- Junk สตริงตามที่กำหนด (จบด้วย &%69=00)
-- ===============================
local function _wrap()
    return "rbxassetid://77402022463860& \n= [[EXD https://create.roblox.com/store/asset/77402022463860/EXD.COM ]]                                                                                          =Y9F%AB%F0%F0%9F%AB%9F%A4%9F%F0%A0%A7%94%F0%AB%90%9F%A4%AB%9F%9F%F0%A4%94%F0%9F%A7%F0%AB%90%A4%A0%F0%9F%F0%9F%AB%9F%9F%A0%94%F0%F0%A7%F0%A4%90%A4%AB%9F%F0%90%AB%A7%AB%F0%9F%9F%A0%9F%94%9F%F0%A4%F0%A4%E2%80%AE&%00&%00%E2%80%AE&%69%64=00%38%33%32%36%30%31%31%39%39%34%38%36%39%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%39%34%36%32%36%31%38%30%33%39%36%35%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%39%33%32%38%32%39%33%34%37%34%34%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%36%38%30%30%35%37%37%32%36%34%30%31%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%30%33%30%38%32%39%38%35%31%37%35%33%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%36%32%38%33%37%31%33%36%33%37%34%39&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%38%34%38%32%30%31%39%38%31%39%30%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%30%37%36%39%31%36%34%32%31%36%38%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%36%38%37%32%39%35%35%39%37%30%32%35%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%39%30%34%33%38%32%37%39%39%32%30%33%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%37%30%35%38%30%39%39%38%32%36%38%36%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%31%33%32%30%38%32%35%37%37%32%37%36%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%36%38%31%34%37%31%35%36%32%31%32%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%35%32%33%38%33%38%34%39%34%34%36%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%38%37%36%33%39%35%39%32%30%37%36%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%33%38%34%31%35%33%33%36%37%30%36%32%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%30%35%36%37%36%35%34%39%33%33%35%34%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%34%32%34%37%34%37%33%38%37%35%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%32%35%38%33%39%37%32%30%34%32%30%36%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%39%36%38%38%30%32%30%31%37%38%35%39%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%35%33%32%39%35%39%35%31%33%31%30%37%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%33%33%38%39%31%38%32%35%36%39%36%32&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%32%36%30%31%31%39%39%34%38%36%39%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%39%34%36%32%36%31%38%30%33%39%36%35%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%39%33%32%38%32%39%33%34%37%34%34%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%36%38%30%30%35%37%37%32%36%34%30%31%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%30%33%30%38%32%39%38%35%31%37%35%33%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%36%32%38%33%37%31%33%36%33%37%34%39&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%38%34%38%32%30%31%39%38%31%39%30%30&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%30%37%36%39%31%36%34%32%31%36%38%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%36%38%37%32%39%35%35%39%37%30%32%35%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%39%30%34%33%38%32%37%39%39%32%30%33%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%37%30%35%38%30%39%39%38%32%36%38%36%37&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%31%33%32%30%38%32%35%37%37%32%37%36%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%36%38%31%34%37%31%35%36%32%31%32%31&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%34%35%32%33%38%33%38%34%39%34%34%36%34&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%33%38%37%36%33%39%35%39%32%30%37%36%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%33%38%34%31%35%33%33%36%37%30%36%32%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%30%35%36%37%36%35%34%39%33%33%35%34%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%37%34%32%34%37%34%37%33%38%37%35%32%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%31%32%35%38%33%39%37%32%30%34%32%30%36%33&%00&%00&%00&%00%E2%80%AE&%69%64=00%37%39%36%38%38%30%32%30%31%37%38%35%39%36&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%32%35%33%32%39%35%39%35%31%33%31%30%37%38&%00&%00&%00&%00%E2%80%AE&%69%64=00%39%33%33%33%38%39%31%38%32%35%36%39%36%32&%00&%00&%00&%00%E2%80%AE&%69%64=00%38%33%32%36%30%31%31%39%39%34%38%36%39%35&%00&%00&%00&%00%E2%80%AE&%69%64=00%31%30%39%34%36%32%36%31%38%30%33%39%36%35%30&%00&%00&%00&%00%E2%80%AE&%69=00"
end

-- ===============================
-- ฟังก์ชันเข้ารหัส 2 ชั้น: ID (decimal/hex) -> hex string (0x...) -> URL encode
-- ===============================
local function encodeRealId(rawInput)
    -- 1. ตัดช่องว่าง
    rawInput = string.gsub(rawInput, "^%s*(.-)%s*$", "%1")
    if rawInput == "" then return nil end
    
    -- 2. แปลง ID ที่กรอกให้เป็นเลขฐานสิบ (รองรับทั้ง 0x... และตัวเลขทั่วไป)
    local decimal = nil
    if string.sub(rawInput, 1, 2):lower() == "0x" then
        decimal = tonumber(rawInput)
    else
        decimal = tonumber(rawInput)
    end
    if not decimal then return nil end
    
    -- 3. แปลงเลขฐานสิบเป็น hexadecimal string แบบมี 0x นำหน้า (ตัวพิมพ์ใหญ่)
    local hexString = string.format("0x%X", decimal)
    
    -- 4. URL encode แต่ละอักขระของ hexString
    local function urlEncodeChar(c)
        return string.format("%%%02X", string.byte(c))
    end
    
    local encoded = ""
    for i = 1, #hexString do
        encoded = encoded .. urlEncodeChar(string.sub(hexString, i, i))
    end
    
    return encoded
end

-- ===============================
-- ฟังก์ชันสร้าง Payload: Junk + ไอดีจริงที่ถูกเข้ารหัสแล้ว
-- ===============================
local function buildPayload(encodedRealId)
    return _wrap() .. encodedRealId
end

-- ===============================
-- Preview ชื่อเพลง (ใช้เลขฐานสิบที่แปลงได้ในการดึงข้อมูล)
-- ===============================
local function tryGetProductInfo(rawInput)
    local decimal = nil
    if string.sub(rawInput, 1, 2):lower() == "0x" then
        decimal = tonumber(rawInput)
    else
        decimal = tonumber(rawInput)
    end
    if not decimal then return nil, "Invalid ID format" end
    return pcall(function()
        return MarketplaceService:GetProductInfo(decimal)
    end)
end

-- ===============================
-- GUI (เหมือนเดิม)
-- ===============================
local _g = Instance.new("\83\99\114\101\101\110\71\117\105")
_g.Name = "\77\117\115\105\99\80\97\110\101\108"
_g.ResetOnSpawn = false
_g.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
_g.Parent = player[D[7]]

local _fr = Instance.new("\70\114\97\109\101")
_fr.Size = UDim2.new(0,300,0,210)
_fr.Position = UDim2.new(0.5,-150,0.5,-105)
_fr.BackgroundColor3 = Color3.fromRGB(8,8,16)
_fr.BorderSizePixel = 0
_fr.Active = true
_fr.Parent = _g
Instance.new("\85\73\67\111\114\110\101\114",_fr).CornerRadius = UDim.new(0,12)
local _frs = Instance.new("\85\73\83\116\114\111\107\101",_fr)
_frs.Color = Color3.fromRGB(25,25,55)
_frs.Thickness = 1.5

local _tb = Instance.new("\70\114\97\109\101",_fr)
_tb.Size = UDim2.new(1,0,0,44)
_tb.BackgroundColor3 = Color3.fromRGB(10,10,20)
_tb.BorderSizePixel = 0
Instance.new("\85\73\67\111\114\110\101\114",_tb).CornerRadius = UDim.new(0,12)
local _tbf = Instance.new("\70\114\97\109\101",_tb)
_tbf.Size = UDim2.new(1,0,0.5,0)
_tbf.Position = UDim2.new(0,0,0.5,0)
_tbf.BackgroundColor3 = Color3.fromRGB(10,10,20)
_tbf.BorderSizePixel = 0

local _dot1 = Instance.new("\70\114\97\109\101",_tb)
_dot1.Size = UDim2.new(0,8,0,8)
_dot1.Position = UDim2.new(0,12,0.5,-4)
_dot1.BackgroundColor3 = Color3.fromRGB(255,60,60)
_dot1.BorderSizePixel = 0
Instance.new("\85\73\67\111\114\110\101\114",_dot1).CornerRadius = UDim.new(1,0)

local _dot2 = Instance.new("\70\114\97\109\101",_tb)
_dot2.Size = UDim2.new(0,8,0,8)
_dot2.Position = UDim2.new(0,26,0.5,-4)
_dot2.BackgroundColor3 = Color3.fromRGB(255,170,0)
_dot2.BorderSizePixel = 0
Instance.new("\85\73\67\111\114\110\101\114",_dot2).CornerRadius = UDim.new(1,0)

local _dot3 = Instance.new("\70\114\97\109\101",_tb)
_dot3.Size = UDim2.new(0,8,0,8)
_dot3.Position = UDim2.new(0,40,0.5,-4)
_dot3.BackgroundColor3 = Color3.fromRGB(0,200,80)
_dot3.BorderSizePixel = 0
Instance.new("\85\73\67\111\114\110\101\114",_dot3).CornerRadius = UDim.new(1,0)

local _tt = Instance.new("\84\101\120\116\76\97\98\101\108",_tb)
_tt.Size = UDim2.new(1,-80,1,0)
_tt.Position = UDim2.new(0,60,0,0)
_tt.BackgroundTransparency = 1
_tt.Text = "MUSIC // 191"
_tt.TextColor3 = Color3.fromRGB(80,80,160)
_tt.TextSize = 13
_tt.Font = Enum.Font.Code
_tt.TextXAlignment = Enum.TextXAlignment.Left

local _cb = Instance.new("\84\101\120\116\66\117\116\116\111\110",_tb)
_cb.Size = UDim2.new(0,24,0,24)
_cb.Position = UDim2.new(1,-32,0.5,-12)
_cb.BackgroundColor3 = Color3.fromRGB(15,15,30)
_cb.Text = "✕"
_cb.TextColor3 = Color3.fromRGB(80,80,120)
_cb.TextSize = 12
_cb.Font = Enum.Font.GothamBold
_cb.BorderSizePixel = 0
Instance.new("\85\73\67\111\114\110\101\114",_cb).CornerRadius = UDim.new(0,6)
local _cbs = Instance.new("\85\73\83\116\114\111\107\101",_cb)
_cbs.Color = Color3.fromRGB(40,40,80)
_cbs.Thickness = 1

local _sl = Instance.new("\84\101\120\116\76\97\98\101\108",_fr)
_sl.Size = UDim2.new(1,-24,0,28)
_sl.Position = UDim2.new(0,12,0,52)
_sl.BackgroundColor3 = Color3.fromRGB(5,5,12)
_sl.BorderSizePixel = 0
_sl.Text = "◈  AWAITING INPUT..."
_sl.TextColor3 = Color3.fromRGB(60,60,120)
_sl.TextSize = 11
_sl.Font = Enum.Font.Code
_sl.TextXAlignment = Enum.TextXAlignment.Left
_sl.TextTruncate = Enum.TextTruncate.AtEnd
Instance.new("\85\73\67\111\114\110\101\114",_sl).CornerRadius = UDim.new(0,6)
local _sls = Instance.new("\85\73\83\116\114\111\107\101",_sl)
_sls.Color = Color3.fromRGB(20,20,45)
_sls.Thickness = 1

local _il = Instance.new("\84\101\120\116\76\97\98\101\108",_fr)
_il.Size = UDim2.new(1,-24,0,14)
_il.Position = UDim2.new(0,14,0,88)
_il.BackgroundTransparency = 1
_il.Text = "// SOUND_ID"
_il.TextColor3 = Color3.fromRGB(40,40,80)
_il.TextSize = 10
_il.Font = Enum.Font.Code
_il.TextXAlignment = Enum.TextXAlignment.Left

local _ib = Instance.new("\84\101\120\116\66\111\120",_fr)
_ib.Size = UDim2.new(1,-24,0,34)
_ib.Position = UDim2.new(0,12,0,104)
_ib.BackgroundColor3 = Color3.fromRGB(5,5,12)
_ib.BorderSizePixel = 0
_ib.Text = ""
_ib.PlaceholderText = "Sound ID (decimal or hex)"
_ib.PlaceholderColor3 = Color3.fromRGB(25,25,50)
_ib.TextColor3 = Color3.fromRGB(100,100,200)
_ib.TextSize = 13
_ib.Font = Enum.Font.Code
_ib.ClearTextOnFocus = false
Instance.new("\85\73\67\111\114\110\101\114",_ib).CornerRadius = UDim.new(0,6)
local _ibs = Instance.new("\85\73\83\116\114\111\107\101",_ib)
_ibs.Color = Color3.fromRGB(25,25,55)
_ibs.Thickness = 1

local _pb = Instance.new("\84\101\120\116\66\117\116\116\111\110",_fr)
_pb.Size = UDim2.new(1,-24,0,36)
_pb.Position = UDim2.new(0,12,0,148)
_pb.BackgroundColor3 = Color3.fromRGB(10,10,22)
_pb.Text = "▶  EXECUTE"
_pb.TextColor3 = Color3.fromRGB(80,80,160)
_pb.TextSize = 13
_pb.Font = Enum.Font.Code
_pb.BorderSizePixel = 0
Instance.new("\85\73\67\111\114\110\101\114",_pb).CornerRadius = UDim.new(0,8)
local _pbs = Instance.new("\85\73\83\116\114\111\107\101",_pb)
_pbs.Color = Color3.fromRGB(25,25,55)
_pbs.Thickness = 1

-- ===============================
-- Drag
-- ===============================
local _dr, _ds, _dp = false, nil, nil
_tb.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        _dr = true; _ds = i.Position; _dp = _fr.Position
    end
end)
UserInputService.InputChanged:Connect(function(i)
    if _dr and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local d = i.Position - _ds
        _fr.Position = UDim2.new(_dp.X.Scale,_dp.X.Offset+d.X,_dp.Y.Scale,_dp.Y.Offset+d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then _dr = false end
end)

-- ===============================
-- Preview ชื่อเพลง (ใช้เลขฐานสิบที่แปลงได้)
-- ===============================
_ib:GetPropertyChangedSignal("\84\101\120\116"):Connect(function()
    local raw = _ib.Text
    if #raw > 3 then
        _sl.Text = "⏳  FETCHING..."
        _sl.TextColor3 = Color3.fromRGB(60,60,120)
        task.spawn(function()
            local ok, info = tryGetProductInfo(raw)
            if ok and info then
                _sl.Text = "◈  " .. (info.Name or "UNKNOWN")
                _sl.TextColor3 = Color3.fromRGB(40,180,100)
            else
                _sl.Text = "◈  PREVIEW UNAVAILABLE"
                _sl.TextColor3 = Color3.fromRGB(160,40,40)
            end
        end)
    else
        _sl.Text = "◈  AWAITING INPUT..."
        _sl.TextColor3 = Color3.fromRGB(60,60,120)
    end
end)

-- ===============================
-- เล่นเพลง (เข้ารหัส 2 ชั้นก่อนส่ง)
-- ===============================
local function playMusic()
    local rawId = _ib.Text
    if rawId == "" then
        _pb.Text = "⚠  NO INPUT"
        _pb.TextColor3 = Color3.fromRGB(180,40,40)
        task.wait(1.5)
        _pb.Text = "▶  EXECUTE"
        _pb.TextColor3 = Color3.fromRGB(80,80,160)
        return
    end

    local encoded = encodeRealId(rawId)
    if not encoded then
        _pb.Text = "⚠  INVALID ID"
        _pb.TextColor3 = Color3.fromRGB(180,40,40)
        task.wait(1.5)
        _pb.Text = "▶  EXECUTE"
        _pb.TextColor3 = Color3.fromRGB(80,80,160)
        return
    end

    local payload = buildPayload(encoded)
    _reB:FireServer(D[15], payload, true)

    _pb.Text = "■  PLAYING..."
    _pb.TextColor3 = Color3.fromRGB(40,180,100)
    _pbs.Color = Color3.fromRGB(20,80,40)
    TweenService:Create(_pb, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(8,20,12)}):Play()
    task.wait(0.15)
    TweenService:Create(_pb, TweenInfo.new(0.1), {BackgroundColor3 = Color3.fromRGB(10,10,22)}):Play()
    task.wait(0.5)
    _pb.Text = "▶  EXECUTE"
    _pb.TextColor3 = Color3.fromRGB(80,80,160)
    _pbs.Color = Color3.fromRGB(25,25,55)
end

_pb.MouseButton1Click:Connect(playMusic)

-- ===============================
-- Close
-- ===============================
_cb.MouseButton1Click:Connect(function()
    _cb.TextColor3 = Color3.fromRGB(255,60,60)
    TweenService:Create(_fr, TweenInfo.new(0.2, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0,0,0,0),
        Position = UDim2.new(_fr.Position.X.Scale, _fr.Position.X.Offset+150, _fr.Position.Y.Scale, _fr.Position.Y.Offset+105)
    }):Play()
    task.wait(0.25)
    _g:Destroy()
end)
