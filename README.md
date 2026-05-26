--// ESP Nick Simples (LocalScript)
-- Coloque em StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local function criarESP(player)
	if player == LocalPlayer then
		return
	end

	local function adicionar(char)
		local head = char:WaitForChild("Head", 5)
		if not head then
			return
		end

		-- Remove antigo ESP se existir
		if head:FindFirstChild("NickESP") then
			head.NickESP:Destroy()
		end

		local billboard = Instance.new("BillboardGui")
		billboard.Name = "NickESP"
		billboard.Size = UDim2.new(0, 200, 0, 50)
		billboard.StudsOffset = Vector3.new(0, 2.5, 0)
		billboard.AlwaysOnTop = true
		billboard.Parent = head

		local text = Instance.new("TextLabel")
		text.Size = UDim2.new(1, 0, 1, 0)
		text.BackgroundTransparency = 1
		text.Text = player.Name
		text.TextColor3 = Color3.fromRGB(0, 255, 0)
		text.TextStrokeTransparency = 0
		text.TextScaled = true
		text.Font = Enum.Font.SourceSansBold
		text.Parent = billboard
	end

	if player.Character then
		adicionar(player.Character)
	end

	player.CharacterAdded:Connect(adicionar)
end

for _, player in ipairs(Players:GetPlayers()) do
	criarESP(player)
end

Players.PlayerAdded:Connect(criarESP)
