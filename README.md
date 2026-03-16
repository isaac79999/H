local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LP = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui")
gui.Parent = gethui()

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,170,0,60)
frame.Position = UDim2.new(0.4,0,0.4,0)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.Active = true
frame.Draggable = true
frame.Parent = gui

local button = Instance.new("TextButton")
button.Size = UDim2.new(1,-10,1,-10)
button.Position = UDim2.new(0,5,0,5)
button.Text = "Silent Aim OFF"
button.BackgroundColor3 = Color3.fromRGB(60,60,60)
button.TextColor3 = Color3.new(1,1,1)
button.Parent = frame

local enabled = false
local grabbedHead = nil

button.MouseButton1Click:Connect(function()
	enabled = not enabled
	button.Text = enabled and "Silent Aim ON" or "Silent Aim OFF"
end)

-- pegar player mais próximo
local function getClosestPlayer()

	local char = LP.Character
	if not char then return end
	
	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end
	
	local closest
	local dist = math.huge
	
	for _,plr in pairs(Players:GetPlayers()) do
		
		if plr ~= LP and plr.Character then
			
			local head = plr.Character:FindFirstChild("Head")
			local hum = plr.Character:FindFirstChild("Humanoid")
			local root2 = plr.Character:FindFirstChild("HumanoidRootPart")
			
			if head and hum and root2 and hum.Health > 0 then
				
				if not root2:FindFirstChild("SafeGui") then
					
					local d = (head.Position - root.Position).Magnitude
					
					if d < dist then
						dist = d
						closest = plr
					end
					
				end
				
			end
			
		end
		
	end
	
	return closest
end

RunService.RenderStepped:Connect(function()

	if not enabled then return end

	local char = LP.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	local target = getClosestPlayer()
	if not target then return end

	local head = target.Character and target.Character:FindFirstChild("Head")
	if not head then return end

	grabbedHead = head

	-- aumentar hitbox
	head.Size = Vector3.new(8,8,8)
	head.Transparency = 1
	head.CanCollide = false

	-- posição na sua frente
	local pos = root.Position + root.CFrame.LookVector * 6
	head.CFrame = CFrame.new(pos)

end)
