# Fox-speed- --[[
⚡ Speed + Jump + Fly + ShiftLock GUI (Estilo rápido B)
💾 Agora com salvamento automático ao renascer
Autor: GPT
--]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-- Configurações
local MAX_SPEED, MIN_SPEED, DEFAULT_SPEED = 200, 0, 16
local MAX_JUMP, MIN_JUMP, DEFAULT_JUMP = 300, 50, 50
local DEFAULT_FLY = 100

-- Estado salvo
local saved = {
	Speed = DEFAULT_SPEED,
	Jump = DEFAULT_JUMP,
	Fly = false,
	FlySpeed = DEFAULT_FLY,
	Shift = false
}

local function new(class, props)
	local obj = Instance.new(class)
	for k,v in pairs(props or {}) do obj[k]=v end
	return obj
end

-- GUI principal
local gui = new("ScreenGui", {
	Name = "FlyMenu",
	ResetOnSpawn = false,
	Parent = player:WaitForChild("PlayerGui")
})

local toggle = new("TextButton", {
	Parent = gui,
	Size = UDim2.new(0,120,0,40),
	Position = UDim2.new(0,20,0,20),
	Text = "Abrir Menu",
	BackgroundColor3 = Color3.fromRGB(85,170,255),
	TextColor3 = Color3.new(0,0,0),
	Font = Enum.Font.SourceSansBold,
	TextSize = 18,
	BorderSizePixel = 0
})

local frame = new("Frame", {
	Parent = gui,
	Size = UDim2.new(0,520,0,340),
	Position = UDim2.new(0.5,-260,1,0),
	BackgroundColor3 = Color3.fromRGB(30,30,35),
	Visible = false,
	BorderSizePixel = 0
})

local left = new("Frame", {
	Parent = frame,
	Size = UDim2.new(0,160,1,0),
	BackgroundColor3 = Color3.fromRGB(25,25,28)
})

local right = new("Frame", {
	Parent = frame,
	Size = UDim2.new(1,-160,1,0),
	Position = UDim2.new(0,160,0,0),
	BackgroundColor3 = Color3.fromRGB(40,40,45)
})

local function menuButton(name,text)
	local n = #left:GetChildren()
	local b = new("TextButton", {
		Parent = left,
		Name = name,
		Size = UDim2.new(1,-12,0,36),
		Position = UDim2.new(0,6,0,6+(n-1)*42),
		Text = text,
		BackgroundColor3 = Color3.fromRGB(50,50,55),
		TextColor3 = Color3.new(1,1,1),
		Font = Enum.Font.SourceSans,
		TextSize = 16,
		BorderSizePixel = 0
	})
	return b
end

local speedBtn = menuButton("Speed","Speed")
local jumpBtn = menuButton("Jump","Super Jump")
local flyBtn = menuButton("Fly","Fly")
local shiftBtn = menuButton("Shift","Shift Lock")

-----------------------------
-- SPEED PANEL
-----------------------------
local speedFrame = new("Frame",{Parent=right,Visible=true})
local speedBox = new("TextBox",{
	Parent = speedFrame,
	Size = UDim2.new(0,180,0,36),
	Text = tostring(saved.Speed),
	BackgroundColor3 = Color3.fromRGB(60,60,65),
	TextColor3 = Color3.new(1,1,1),
	Font = Enum.Font.SourceSans,
	TextSize = 18
})
local plus = new("TextButton",{
	Parent = speedFrame,
	Position = UDim2.new(0,190,0,0),
	Size = UDim2.new(0,40,0,36),
	Text = "+",
	BackgroundColor3 = Color3.fromRGB(100,200,100),
	Font = Enum.Font.SourceSansBold,
	TextSize = 24
})
local minus = new("TextButton",{
	Parent = speedFrame,
	Position = UDim2.new(0,235,0,0),
	Size = UDim2.new(0,40,0,36),
	Text = "-",
	BackgroundColor3 = Color3.fromRGB(200,100,100),
	Font = Enum.Font.SourceSansBold,
	TextSize = 24
})
local statusSpeed = new("TextLabel",{
	Parent = speedFrame,
	Position = UDim2.new(0,0,0,50),
	Text = "WalkSpeed = " .. saved.Speed,
	TextColor3 = Color3.new(1,1,1),
	BackgroundTransparency = 1,
	TextXAlignment = Enum.TextXAlignment.Left
})

local function applySpeed(val)
	local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
	local num = tonumber(val)
	if hum and num then
		num = math.clamp(num,MIN_SPEED,MAX_SPEED)
		hum.WalkSpeed = num
		statusSpeed.Text = "WalkSpeed = "..num
		saved.Speed = num
	end
end

speedBox.FocusLost:Connect(function(e)
	if e then applySpeed(speedBox.Text) end
end)
plus.MouseButton1Click:Connect(function()
	local n = math.clamp(tonumber(speedBox.Text or 16)+1,MIN_SPEED,MAX_SPEED)
	speedBox.Text = tostring(n)
	applySpeed(n)
end)
minus.MouseButton1Click:Connect(function()
	local n = math.clamp(tonumber(speedBox.Text or 16)-1,MIN_SPEED,MAX_SPEED)
	speedBox.Text = tostring(n)
	applySpeed(n)
end)

-----------------------------
-- JUMP PANEL
-----------------------------
local jumpFrame = new("Frame",{Parent=right,Visible=false})
local jumpBox = new("TextBox",{
	Parent = jumpFrame,
	Size = UDim2.new(0,180,0,36),
	Text = tostring(saved.Jump),
	BackgroundColor3 = Color3.fromRGB(60,60,65),
	TextColor3 = Color3.new(1,1,1),
	Font = Enum.Font.SourceSans,
	TextSize = 18
})
local applyJumpBtn = new("TextButton",{
	Parent = jumpFrame,
	Size = UDim2.new(0,160,0,36),
	Position = UDim2.new(0,190,0,0),
	Text = "Aplicar",
	BackgroundColor3 = Color3.fromRGB(120,255,120),
	Font = Enum.Font.SourceSansBold,
	TextSize = 16
})
applyJumpBtn.MouseButton1Click:Connect(function()
	local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
	if hum then
		local val = tonumber(jumpBox.Text) or DEFAULT_JUMP
		hum.UseJumpPower = true
		hum.JumpPower = math.clamp(val,MIN_JUMP,MAX_JUMP)
		saved.Jump = val
	end
end)

-----------------------------
-- FLY PANEL (estilo B)
-----------------------------
local flyFrame = new("Frame",{Parent=right,Visible=false})
local flySpeedBox = new("TextBox",{
	Parent = flyFrame,
	Size = UDim2.new(0,180,0,36),
	Text = tostring(saved.FlySpeed),
	BackgroundColor3 = Color3.fromRGB(60,60,65),
	TextColor3 = Color3.new(1,1,1),
	Font = Enum.Font.SourceSans,
	TextSize = 18
})
local toggleFly = new("TextButton",{
	Parent = flyFrame,
	Size = UDim2.new(0,160,0,36),
	Position = UDim2.new(0,190,0,0),
	Text = "Ativar Voo",
	BackgroundColor3 = Color3.fromRGB(255,200,100),
	Font = Enum.Font.SourceSansBold,
	TextSize = 16
})
local flying=false
local flyConn
local bodyGyro,bodyVel

local function startFly()
	if flying then return end
	local char=player.Character
	if not char then return end
	local root=char:WaitForChild("HumanoidRootPart")
	local hum=char:FindFirstChildOfClass("Humanoid")
	hum.PlatformStand=true

	bodyVel=Instance.new("BodyVelocity")
	bodyVel.MaxForce=Vector3.new(9e4,9e4,9e4)
	bodyVel.Parent=root
	bodyGyro=Instance.new("BodyGyro")
	bodyGyro.MaxTorque=Vector3.new(9e4,9e4,9e4)
	bodyGyro.Parent=root

	local flySpeed=tonumber(flySpeedBox.Text) or DEFAULT_FLY
	saved.FlySpeed = flySpeed
	flying=true
	saved.Fly = true

	flyConn=RunService.RenderStepped:Connect(function()
		if not flying or not root then return end
		local cam=workspace.CurrentCamera
		local dir=Vector3.new()
		if UIS:IsKeyDown(Enum.KeyCode.W) then dir=dir+cam.CFrame.LookVector end
		if UIS:IsKeyDown(Enum.KeyCode.S) then dir=dir-cam.CFrame.LookVector end
		if UIS:IsKeyDown(Enum.KeyCode.A) then dir=dir-cam.CFrame.RightVector end
		if UIS:IsKeyDown(Enum.KeyCode.D) then dir=dir+cam.CFrame.RightVector end
		if UIS:IsKeyDown(Enum.KeyCode.Space) then dir=dir+Vector3.new(0,1,0) end
		if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then dir=dir-Vector3.new(0,1,0) end
		if dir.Magnitude>0 then dir=dir.Unit end
		bodyVel.Velocity=dir*flySpeed
		bodyGyro.CFrame=cam.CFrame
	end)
end

local function stopFly()
	flying=false
	saved.Fly=false
	if bodyVel then bodyVel:Destroy() end
	if bodyGyro then bodyGyro:Destroy() end
	if flyConn then flyConn:Disconnect() end
	local hum=player.Character and player.Character:FindFirstChildOfClass("Humanoid")
	if hum then hum.PlatformStand=false end
end

toggleFly.MouseButton1Click:Connect(function()
	if flying then
		stopFly()
		toggleFly.Text="Ativar Voo"
	else
		startFly()
		toggleFly.Text="Desativar Voo"
	end
end)

-----------------------------
-- SHIFT LOCK PANEL
-----------------------------
local shiftFrame = new("Frame",{Parent=right,Visible=false})
local shiftBtn2 = new("TextButton",{
	Parent = shiftFrame,
	Size = UDim2.new(0,200,0,40),
	Text = "Ativar Shift Lock",
	BackgroundColor3 = Color3.fromRGB(255,150,100),
	TextColor3 = Color3.fromRGB(20,20,20),
	Font = Enum.Font.SourceSansBold,
	TextSize = 18
})
local shiftLocked=false
local function enableShift()
	UIS.MouseBehavior=Enum.MouseBehavior.LockCenter
	UIS.MouseIconEnabled=false
	shiftLocked=true
	saved.Shift=true
	shiftBtn2.Text="Desativar Shift Lock"
end
local function disableShift()
	UIS.MouseBehavior=Enum.MouseBehavior.Default
	UIS.MouseIconEnabled=true
	shiftLocked=false
	saved.Shift=false
	shiftBtn2.Text="Ativar Shift Lock"
end
shiftBtn2.MouseButton1Click:Connect(function()
	if shiftLocked then disableShift() else enableShift() end
end)

-----------------------------
-- TROCA DE MENUS
-----------------------------
local function showPanel(name)
	for _,f in ipairs(right:GetChildren()) do if f:IsA("Frame") then f.Visible=false end end
	if name=="Speed" then speedFrame.Visible=true
	elseif name=="Jump" then jumpFrame.Visible=true
	elseif name=="Fly" then flyFrame.Visible=true
	elseif name=="Shift" then shiftFrame.Visible=true end
end

speedBtn.MouseButton1Click:Connect(function() showPanel("Speed") end)
jumpBtn.MouseButton1Click:Connect(function() showPanel("Jump") end)
flyBtn.MouseButton1Click:Connect(function() showPanel("Fly") end)
shiftBtn.MouseButton1Click:Connect(function() showPanel("Shift") end)

-----------------------------
-- MENU ANIMAÇÃO
-----------------------------
local open=false
local tweenInfo=TweenInfo.new(0.4,Enum.EasingStyle.Sine,Enum.EasingDirection.Out)
local function toggleMenu()
	open=not open
	if open then
		frame.Visible=true
		TweenService:Create(frame,tweenInfo,{Position=UDim2.new(0.5,-260,0.5,-160)}):Play()
		toggle.Text="Fechar Menu"
	else
		TweenService:Create(frame,tweenInfo,{Position=UDim2.new(0.5,-260,1,0)}):Play()
		task.wait(0.4)
		frame.Visible=false
		toggle.Text="Abrir Menu"
	end
end
toggle.MouseButton1Click:Connect(toggleMenu)

-----------------------------
-- SALVAR AO RENASCER
-----------------------------
player.CharacterAdded:Connect(function()
	task.wait(0.5)
	applySpeed(saved.Speed)
	local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
	if hum then
		hum.UseJumpPower = true
		hum.JumpPower = saved.Jump
	end
	if saved.Fly then startFly() end
	if saved.Shift then enableShift() end
end)
