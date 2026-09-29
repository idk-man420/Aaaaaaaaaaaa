--// Stage18.Win TP
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local pg = player:WaitForChild("PlayerGui")

pcall(function()
	local o = pg:FindFirstChild("StageWinTP")
	if o then o:Destroy() end
end)

local enabled = false
local loop
local startCF = nil
local atWin = false

local function getNil(name, class)
	if type(getnilinstances) ~= "function" then return end
	name = string.lower(tostring(name))
	for _, v in next, getnilinstances() do
		if v.Name and string.lower(v.Name) == name then
			if not class or v.ClassName == class then
				return v
			end
		end
	end
end

local function getCFrame(inst)
	if not inst then return end
	if inst:IsA("BasePart") then return inst.CFrame end
	if inst:IsA("Model") then
		local ok, cf = pcall(function() return inst:GetPivot() end)
		if ok and cf then return cf end
		if inst.PrimaryPart then return inst.PrimaryPart.CFrame end
		local p = inst:FindFirstChildWhichIsA("BasePart", true)
		if p then return p.CFrame end
	end
	if inst:IsA("Attachment") then return inst.WorldCFrame end
end

local function getWinPart()
	local ugc = getNil("Ugc", "DataModel") or getNil("Ugc") or getNil("UGC")
	if ugc then
		local ok, win = pcall(function()
			local ws = ugc:FindFirstChild("Workspace") or ugc:FindFirstChildOfClass("Workspace")
			if not ws then return end
			local s = ws:FindFirstChild("Stage18", true)
			if not s then return end
			return s:FindFirstChild("Win", true) or s:FindFirstChild("win", true)
		end)
		if ok and win then return win end
	end

	local s18 = workspace:FindFirstChild("Stage18") or workspace:FindFirstChild("Stage18", true)
	if s18 then
		local w = s18:FindFirstChild("Win") or s18:FindFirstChild("Win", true)
		if w then return w end
	end

	if type(getnilinstances) == "function" then
		for _, v in next, getnilinstances() do
			if (v.Name == "Win" or v.Name == "win") and v.Parent and v.Parent.Name == "Stage18" then
				return v
			end
		end
	end

	for _, v in ipairs(workspace:GetDescendants()) do
		if v.Name == "Win" or v.Name == "win" then return v end
	end
	return nil
end

local function getRoot()
	return player.Character and player.Character:FindFirstChild("HumanoidRootPart")
end

local function tp(cf)
	local root = getRoot()
	if not root or not cf then return end
	root.CFrame = cf
	root.AssemblyLinearVelocity = Vector3.zero
	root.AssemblyAngularVelocity = Vector3.zero
end

local function stop()
	enabled = false
	if loop then
		pcall(function() task.cancel(loop) end)
		loop = nil
	end
	if startCF then
		tp(startCF)
	end
	startCF = nil
	atWin = false
end

local function start()
	local root = getRoot()
	if not root then return false end
	local win = getWinPart()
	local winCF = getCFrame(win)
	if not winCF then return false end

	if enabled then stop() end

	startCF = root.CFrame
	enabled = true
	atWin = false

	-- first jump: go to Win
	tp(winCF)
	atWin = true

	loop = task.spawn(function()
		while enabled do
			task.wait(1)
			if not enabled then break end
			if atWin then
				-- back to where you turned it on
				if startCF then tp(startCF) end
				atWin = false
			else
				-- to Win again
				local w = getWinPart()
				local cf = getCFrame(w)
				if cf then tp(cf) end
				atWin = true
			end
		end
	end)
	return true
end

local gui = Instance.new("ScreenGui")
gui.Name = "StageWinTP"
gui.ResetOnSpawn = false
gui.Parent = pg

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(0, 130, 0, 40)
btn.Position = UDim2.new(0.5, -65, 0.8, 0)
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
		return
	end

	if not start() then
		btn.Text = "Not found"
		btn.BackgroundColor3 = Color3.fromRGB(180, 120, 40)
		task.delay(1.5, function()
			if not enabled then
				btn.Text = "TP Win: OFF"
				btn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
			end
		end)
		return
	end

	btn.Text = "TP Win: ON"
	btn.BackgroundColor3 = Color3.fromRGB(40, 170, 90)
end)
