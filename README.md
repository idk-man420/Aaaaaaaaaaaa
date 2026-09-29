--// TP Stage18.Win · ON/OFF
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local pg = player:WaitForChild("PlayerGui")

pcall(function()
	local o = pg:FindFirstChild("StageWinTP")
	if o then o:Destroy() end
end)

local enabled = false
local conn

local function getNil(name, class)
	if not getnilinstances then return end
	for _, v in next, getnilinstances() do
		if v.ClassName == class and v.Name == name then
			return v
		end
	end
end

local function getWinPart()
	local ok, part = pcall(function()
		local ugc = getNil("Ugc", "DataModel")
		if ugc then
			return ugc.Workspace.Stage18.Win
		end
	end)
	if ok and part then return part end

	local ok2, part2 = pcall(function()
		return workspace:FindFirstChild("Stage18") and workspace.Stage18:FindFirstChild("Win")
	end)
	if ok2 and part2 then return part2 end
	return nil
end

local function getPos(inst)
	if not inst then return end
	if inst:IsA("BasePart") then return inst.CFrame end
	if inst:IsA("Model") then
		if inst.PrimaryPart then return inst.PrimaryPart.CFrame end
		local p = inst:FindFirstChildWhichIsA("BasePart", true)
		if p then return p.CFrame end
	end
end

local function stop()
	enabled = false
	if conn then conn:Disconnect() conn = nil end
end

local function start()
	stop()
	enabled = true
	conn = RunService.Heartbeat:Connect(function()
		if not enabled then return end
		local win = getWinPart()
		local cf = getPos(win)
		local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
		if cf and root then
			root.CFrame = cf -- 0 studs offset
			root.AssemblyLinearVelocity = Vector3.zero
		end
	end)
end

local gui = Instance.new("ScreenGui")
gui.Name = "StageWinTP"
gui.ResetOnSpawn = false
gui.Parent = pg

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(0, 110, 0, 40)
btn.Position = UDim2.new(0.5, -55, 0.8, 0)
btn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
btn.Text = "TP Win: OFF"
btn.TextColor3 = Color3.new(1, 1, 1)
btn.Font = Enum.Font.GothamBold
btn.TextSize = 13
btn.Active = true
btn.Draggable = true
btn.Parent = gui
Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)

btn.MouseButton1Click:Connect(function()
	if enabled then
		stop()
		btn.Text = "TP Win: OFF"
		btn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
	else
		local win = getWinPart()
		if not win then
			btn.Text = "Not found"
			btn.BackgroundColor3 = Color3.fromRGB(180, 120, 40)
			task.delay(1.2, function()
				if not enabled then
					btn.Text = "TP Win: OFF"
					btn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
				end
			end)
			return
		end
		start()
		btn.Text = "TP Win: ON"
		btn.BackgroundColor3 = Color3.fromRGB(40, 170, 90)
	end
end)

print("aaaa")
