--[[ ULTIMATE HUB - AJUSTADO LEGIT para exploits ]]
if not game:GetService("CoreGui"):FindFirstChild("UltimateHubUI") then
local gui = Instance.new("ScreenGui")
gui.Name = "UltimateHubUI"
local ok,core = pcall(function() return game:GetService("CoreGui") end)
gui.Parent = ok and core or game.Players.LocalPlayer:WaitForChild("PlayerGui")

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0,340,0,370)
frame.Position = UDim2.new(0.5,-170,0.22,0)
frame.BackgroundColor3 = Color3.fromRGB(35,35,45)
frame.BorderSizePixel = 0
frame.Draggable = true
frame.Active = true

local bar = Instance.new("Frame", frame)
bar.Size = UDim2.new(1,0,0,32)
bar.BackgroundColor3 = Color3.fromRGB(16,20,28)
local title = Instance.new("TextLabel", bar)
title.Text = "UltimateHub LEGIT"
title.Size = UDim2.new(1,0,1,0)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextColor3 = Color3.fromRGB(99,210,255)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Position = UDim2.new(0,13,0,0)

local min = Instance.new("TextButton", bar)
min.Text = "_"
min.Position = UDim2.new(1,-63,0,0)
min.Size = UDim2.new(0,26,1,0)
min.BackgroundTransparency = 1
min.Font = Enum.Font.GothamBold
min.TextSize = 25
min.TextColor3 = Color3.fromRGB(255,215,99)
local minimized = false

local close = Instance.new("TextButton", bar)
close.Text = "✖"
close.Position = UDim2.new(1,-32,0,0)
close.Size = UDim2.new(0,26,1,0)
close.BackgroundTransparency = 1
close.Font = Enum.Font.GothamBold
close.TextSize = 20
close.TextColor3 = Color3.fromRGB(255,92,92)
close.MouseButton1Click:Connect(function() frame.Visible = false end)
min.MouseButton1Click:Connect(function()
	if not minimized then
		frame.Size = UDim2.new(0,340,0,32)
		minimized = true
	else
		frame.Size = UDim2.new(0,340,0,370)
		minimized = false
	end
end)

local status = {}
local labels = {"Aimlock[M2]","GodMode","Flash[F]","Fly[G]","TchauWall[T]","ESP[P]","AutoTP[Y]","Reset[R]"}
for i,v in ipairs(labels) do
	local l = Instance.new("TextLabel", frame)
	l.Name = v
	l.Text = v..": OFF"
	l.Size = UDim2.new(1,-22,0,28)
	l.Position = UDim2.new(0,13,0,36+(i-1)*37)
	l.BorderSizePixel = 0
	l.BackgroundTransparency = 1
	l.Font = Enum.Font.GothamSemibold
	l.TextSize = 16
	l.TextColor3 = Color3.fromRGB(180,235,255)
	l.TextXAlignment = Enum.TextXAlignment.Left
	status[v] = l
end

local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local Players = game:GetService("Players")
local plr = Players.LocalPlayer
local camera = workspace.CurrentCamera
local aimlock, god, flash, fly, tchauwall, esp, autotp = false, true, false, false, false, false, false
local isFlying, flyGyro, flyVel, espDraws, tpCooldown = false, nil, nil, {}, 0

function getClosest()
	local closest, dist = nil, math.huge
	for _,pl in ipairs(Players:GetPlayers()) do
		if pl~=plr and pl.Character and pl.Character:FindFirstChild("HumanoidRootPart") and pl.Character:FindFirstChildOfClass("Humanoid") and pl.Character:FindFirstChildOfClass("Humanoid").Health > 0 then
			local pos,vis = camera:WorldToViewportPoint(pl.Character.HumanoidRootPart.Position)
			local d = (Vector2.new(pos.X,pos.Y)-Vector2.new(plr:GetMouse().X,plr:GetMouse().Y)).Magnitude
			if vis and d<dist then dist= d; closest=pl end
		end
	end
	return closest
end

-- AIMLOCK
UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.UserInputType==Enum.UserInputType.MouseButton2 then
		aimlock=true; status["Aimlock[M2]"].Text="Aimlock[M2]: ON"
	end
end)
UIS.InputEnded:Connect(function(i)
	if i.UserInputType==Enum.UserInputType.MouseButton2 then
		aimlock=false; status["Aimlock[M2]"].Text="Aimlock[M2]: OFF"
	end
end)
RS.RenderStepped:Connect(function()
	if aimlock and frame.Visible then
		local tgt = getClosest()
		if tgt and tgt.Character and tgt.Character:FindFirstChild("HumanoidRootPart") then
			camera.CFrame = CFrame.new(camera.CFrame.Position, tgt.Character.HumanoidRootPart.Position)
		end
	end
end)
-- GOD
local function god()
	local c = plr.Character
	if c then
		local h = c:FindFirstChildOfClass("Humanoid")
		if h then
			h.Health = h.MaxHealth
			h:GetPropertyChangedSignal("Health"):Connect(function()
				if god and h.Health < h.MaxHealth then h.Health=h.MaxHealth end
			end)
		end
	end
	status["GodMode"].Text="GodMode: ON"
	status["GodMode"].TextColor3=Color3.fromRGB(60,255,90)
end
Players.LocalPlayer.CharacterAdded:Connect(god)
god()
-- FLASH
UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.KeyCode==Enum.KeyCode.F then
		flash=not flash
		local c,h=plr.Character,plr.Character and plr.Character:FindFirstChildOfClass("Humanoid")
		if h then h.WalkSpeed=flash and 46 or 16 end
		status["Flash[F]"].Text="Flash[F]:"..(flash and "ON" or "OFF")
	end
end)
-- FLY
local flyCon = nil
UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.KeyCode==Enum.KeyCode.G then
		fly=not fly
		local c=plr.Character
		local root=c and c:FindFirstChild("HumanoidRootPart")
		if fly and root then
			flyGyro=Instance.new("BodyGyro",root)
			flyGyro.P=90000;flyGyro.MaxTorque=Vector3.new(9e5,9e5,9e5)
			flyVel=Instance.new("BodyVelocity",root)
			flyVel.MaxForce=Vector3.new(9e4,9e4,9e4)
			flyVel.P=34000
			isFlying=true
			if flyCon then pcall(function() flyCon:Disconnect() end) end
			flyCon = RS.RenderStepped:Connect(function()
				if not isFlying or not plr.Character or not root then return end
				local cam=workspace.CurrentCamera.CFrame
				local v=Vector3.new()
				if UIS:IsKeyDown(Enum.KeyCode.W) then v=v+cam.LookVector end
				if UIS:IsKeyDown(Enum.KeyCode.S) then v=v-cam.LookVector end
				if UIS:IsKeyDown(Enum.KeyCode.A) then v=v-cam.RightVector end
				if UIS:IsKeyDown(Enum.KeyCode.D) then v=v+cam.RightVector end
				if UIS:IsKeyDown(Enum.KeyCode.Space) then v=v+cam.UpVector end
				if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then v=v-cam.UpVector end
				v = v.Magnitude > 0 and v.Unit*46 or Vector3.new()
				flyVel.Velocity = isFlying and v or Vector3.new()
				flyGyro.CFrame=cam
			end)
			status["Fly[G]"].Text="Fly[G]:ON"
		else
			isFlying=false
			if flyGyro then pcall(function() flyGyro:Destroy() end) end
			if flyVel then pcall(function() flyVel:Destroy() end) end
			if flyCon then pcall(function() flyCon:Disconnect() end) end
			status["Fly[G]"].Text="Fly[G]:OFF"
		end
	end
end)
Players.LocalPlayer.CharacterAdded:Connect(function() if fly then isFlying=false end end)

-- TCHAU WALL
function isGoodWall(part)
	if not part:IsA("BasePart") or not part.CanCollide then return false end
	local n=part.Name:lower()
	-- ignora chão
	for _,chao in ipairs({"baseplate","ground","floor","terra","spawn"}) do
		if n:find(chao) then return false end
	end
	-- ignora partes pequenas (detalhes, props)
	if part.Size.Y < 3 and part.Size.X < 3 and part.Size.Z < 3 then return false end
	-- só parede vertical
	if part.Size.Y > part.Size.Z and part.Size.Y > part.Size.X then return true end
	if n:find("wall") or n:find("brick") then return true end
	return false
end

local oldTransparency = setmetatable({}, {__mode="k"})
UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.KeyCode==Enum.KeyCode.T then
		tchauwall = not tchauwall
		status["TchauWall[T]"].Text="TchauWall[T]:"..(tchauwall and "ON" or "OFF")
		if tchauwall then
			spawn(function()
				while tchauwall do
					for _,w in ipairs(workspace:GetDescendants()) do
						if isGoodWall(w) then
							-- NÃO mexe no chão!!!
							oldTransparency[w]=w.Transparency
							pcall(function()
								if w:FindFirstChild("TchauWall") then return end
								Instance.new("BoolValue",w).Name="TchauWall"
							end)
						end
					end
					for _,proj in ipairs(workspace:GetDescendants()) do
						if proj:IsA("BasePart") and (proj.Name:lower():find("bullet") or proj.Name:lower():find("projectile") or proj.Name:lower():find("ray")) then
							pcall(function()
								proj.CanCollide = false
								proj.Material = Enum.Material.ForceField
								proj.Transparency = 0.28
							end)
						end
					end
					wait(0.17)
				end
			end)
		else
			for w,_ in pairs(oldTransparency) do
				pcall(function() if w and w.Parent then w.Transparency=oldTransparency[w] end end)
			end
		end
	end
end)

-- ESP LEGIT
function removeESP()
	for _,draw in pairs(espDraws) do pcall(function() draw:Destroy() end) end
	espDraws = {}
end

function makeESP(ply)
	if not ply.Character or not ply.Character:FindFirstChild("Head") then return end
	if espDraws[ply] then pcall(function() espDraws[ply]:Destroy() end) end
	local box = Instance.new("BillboardGui")
	box.Adornee = ply.Character.Head
	box.AlwaysOnTop = true
	box.Size = UDim2.new(0,100,0,36)
	box.Name = "UHEsp"
	box.Parent = gui
	local color = Color3.fromHSV((ply.UserId%13)/13,0.8,0.8)
	local name = Instance.new("TextLabel",box)
	name.Size = UDim2.new(1,0,0.5,0)
	name.Position = UDim2.new(0,0,0,0)
	name.Font = Enum.Font.GothamBold
	name.TextSize = 16
	name.BackgroundTransparency = 1
	name.TextColor3 = color
	name.Text = ply.Name
	name.TextStrokeTransparency = 0.6
	local dist = Instance.new("TextLabel",box)
	dist.Size = UDim2.new(1,0,0.5,0)
	dist.Position = UDim2.new(0,0,0.5,0)
	dist.Font = Enum.Font.Gotham
	dist.TextSize = 13
	dist.BackgroundTransparency = 1
	dist.TextColor3 = Color3.new(1,1,1)
	dist.TextStrokeTransparency = 0.8
	dist.Text = "..."
	espDraws[ply]=box
end

function updateESPs()
	for _,other in ipairs(Players:GetPlayers()) do
		if other ~= plr and other.Character and other.Character:FindFirstChild("Head") and other.Character:FindFirstChildOfClass("Humanoid") and other.Character:FindFirstChildOfClass("Humanoid").Health > 0 then
			makeESP(other)
			local head = other.Character.Head
			local box = espDraws[other]
			if box and head and box.Parent==gui then
				local dist = math.floor((plr.Character and plr.Character:FindFirstChild("Head") and (plr.Character.Head.Position - head.Position).Magnitude) or 0)
				box.TextLabel.Text = other.Name
				box.TextLabel.TextColor3 = Color3.fromHSV((other.UserId%13)/13,0.8,0.9)
				local lbl2 = box:GetChildren()[2] -- distance label
				lbl2.Text = "Dist: "..dist
			end
		else
			if espDraws[other] then pcall(function() espDraws[other]:Destroy() end) espDraws[other]=nil end
		end
	end
end

UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.KeyCode==Enum.KeyCode.P then
		esp = not esp
		status["ESP[P]"].Text="ESP[P]:"..(esp and "ON" or "OFF")
		if esp then
			RS:BindToRenderStep("u_esp",310,updateESPs)
		else
			RS:UnbindFromRenderStep("u_esp")
			removeESP()
		end
	end
end)

-- AUTO TP LEGIT
function doAutoTp()
	if tick() < (tpCooldown or 0) then return end
	local tgt = getClosest()
	tpCooldown = tick()+2.2
	if tgt and tgt.Character and tgt.Character:FindFirstChild("HumanoidRootPart") and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
		local hrp = tgt.Character.HumanoidRootPart
		local offset = hrp.CFrame.LookVector*2 + Vector3.new(0,1,0)
		plr.Character.HumanoidRootPart.CFrame = hrp.CFrame + offset
	end
end
UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.KeyCode==Enum.KeyCode.Y then
		autotp = not autotp
		status["AutoTP[Y]"].Text="AutoTP[Y]:"..(autotp and "ON" or "OFF")
		if autotp then
			spawn(function()
				while autotp do
					doAutoTp()
					wait(2.3)
				end
			end)
		end
	end
end)
-- RESET
UIS.InputBegan:Connect(function(i,gpe)
	if gpe or not frame.Visible then return end
	if i.KeyCode == Enum.KeyCode.R then
		local c=plr.Character
		if c and c:FindFirstChild("HumanoidRootPart") then
			c:MoveTo(c.HumanoidRootPart.Position + Vector3.new(0,14,0))
		end
	end
end)
-- HUB TOGGLE
UIS.InputBegan:Connect(function(ev)
	if ev.KeyCode==Enum.KeyCode.Insert then
		frame.Visible=not frame.Visible
	end
end)
end
