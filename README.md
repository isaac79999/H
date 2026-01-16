-- BUG FLY + TELEPORT (INTENCIONAL)
-- UI MOBILE | DELTA ROBLOX

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local gui = gethui()

-- ================= UI =================
local sg = Instance.new("ScreenGui", gui)
sg.Name = "BugFlyUI"
sg.ResetOnSpawn = false

local frame = Instance.new("Frame", sg)
frame.Size = UDim2.fromOffset(170, 60)
frame.Position = UDim2.fromScale(0.35, 0.75)
frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
frame.Active = true
frame.Draggable = true
frame.BorderSizePixel = 0
Instance.new("UICorner", frame)

local btn = Instance.new("TextButton", frame)
btn.Size = UDim2.new(1, -10, 1, -10)
btn.Position = UDim2.fromOffset(5,5)
btn.Text = "💥 BUG FLY"
btn.TextScaled = true
btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
btn.TextColor3 = Color3.new(1,1,1)
btn.BorderSizePixel = 0
Instance.new("UICorner", btn)

-- ================= VARS =================
local SPEED = 65
local savedCFrame = nil
local flyConn = nil

-- ================= FLY BUG =================
local function ativarFlyBug()
	local char = player.Character or player.CharacterAdded:Wait()
	local humanoid = char:WaitForChild("Humanoid")
	local root = char:WaitForChild("HumanoidRootPart")

	-- FORÇA BUG
	humanoid.PlatformStand = true

	local lv = Instance.new("LinearVelocity")
	lv.Attachment0 = root:WaitForChild("RootAttachment")
	lv.MaxForce = math.huge
	lv.RelativeTo = Enum.ActuatorRelativeTo.World
	lv.Parent = root

	local bg = Instance.new("BodyGyro")
	bg.MaxTorque = Vector3.new(0, math.huge, 0)
	bg.P = 20000
	bg.Parent = root

	if flyConn then flyConn:Disconnect() end
	flyConn = RunService.RenderStepped:Connect(function()
		if not char or not root or not humanoid then return end

		local moveDir = humanoid.MoveDirection
		if moveDir.Magnitude > 0 then
			local camCF = camera.CFrame
			local finalDir =
				(camCF.LookVector * moveDir.Z) +
				(camCF.RightVector * moveDir.X)

			lv.VectorVelocity = finalDir.Unit * SPEED
			bg.CFrame = CFrame.new(
				root.Position,
				root.Position + Vector3.new(camCF.LookVector.X, 0, camCF.LookVector.Z)
			)
		else
			lv.VectorVelocity = Vector3.zero
		end
	end)
end

-- ================= BOTÃO =================
btn.MouseButton1Click:Connect(function()
	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:WaitForChild("HumanoidRootPart")

	-- 1️⃣ salva posição
	savedCFrame = root.CFrame

	-- 2️⃣ ativa bug (fly)
	ativarFlyBug()

	-- 3️⃣ espera respawn bugado
	player.CharacterAdded:Wait()
	task.wait(0.1)

	-- 4️⃣ teleporta pro local salvo
	local newChar = player.Character
	local newRoot = newChar:WaitForChild("HumanoidRootPart")
	newRoot.CFrame = savedCFrame
end)
