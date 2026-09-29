
local player = game:GetService("Players").LocalPlayer
local pg = player:WaitForChild("PlayerGui")

pcall(function()
	local o = pg:FindFirstChild("FakeVirus")
	if o then o:Destroy() end
end)

local gui = Instance.new("ScreenGui")
gui.Name = "FakeVirus"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.DisplayOrder = 999
gui.Parent = pg

local bg = Instance.new("Frame")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
bg.BorderSizePixel = 0
bg.Parent = gui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -40, 0, 50)
title.Position = UDim2.new(0, 20, 0.25, 0)
title.BackgroundTransparency = 1
title.Text = "⚠️ SYSTEM ALERT"
title.TextColor3 = Color3.fromRGB(255, 40, 40)
title.Font = Enum.Font.GothamBold
title.TextSize = 32
title.Parent = bg

local body = Instance.new("TextLabel")
body.Size = UDim2.new(1, -40, 0, 120)
body.Position = UDim2.new(0, 20, 0.35, 0)
body.BackgroundTransparency = 1
body.Text = "Critical process failed.\nUnauthorized access detected.\n(This is a FAKE prank — nothing is wrong.)"
body.TextColor3 = Color3.fromRGB(220, 220, 220)
body.Font = Enum.Font.Gotham
body.TextSize = 18
body.TextWrapped = true
body.Parent = bg

local barBg = Instance.new("Frame")
barBg.Size = UDim2.new(0.6, 0, 0, 18)
barBg.Position = UDim2.new(0.2, 0, 0.55, 0)
barBg.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
barBg.Parent = bg
Instance.new("UICorner", barBg).CornerRadius = UDim.new(0, 4)

local bar = Instance.new("Frame")
bar.Size = UDim2.new(0, 0, 1, 0)
bar.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
bar.Parent = barBg
Instance.new("UICorner", bar).CornerRadius = UDim.new(0, 4)

local pct = Instance.new("TextLabel")
pct.Size = UDim2.new(1, 0, 0, 24)
pct.Position = UDim2.new(0, 0, 0.6, 0)
pct.BackgroundTransparency = 1
pct.Text = "Loading 0%"
pct.TextColor3 = Color3.fromRGB(200, 200, 200)
pct.Font = Enum.Font.Gotham
pct.TextSize = 14
pct.Parent = bg

local timer = Instance.new("TextLabel")
timer.Size = UDim2.new(1, 0, 0, 24)
timer.Position = UDim2.new(0, 0, 0.72, 0)
timer.BackgroundTransparency = 1
timer.Text = "Closes in 10s"
timer.TextColor3 = Color3.fromRGB(160, 160, 160)
timer.Font = Enum.Font.Gotham
timer.TextSize = 14
timer.Parent = bg
task.spawn(function()
	for i = 0, 100 do
		if not gui.Parent then return end
		bar.Size = UDim2.new(i / 100, 0, 1, 0)
		pct.Text = "Loading " .. i .. "%"
		task.wait(0.03)
	end
	if gui.Parent then pct.Text = "Prank complete — you're fine." end
end)

task.spawn(function()
	for s = 10, 1, -1 do
		if not gui.Parent then return end
		timer.Text = "Closes in " .. s .. "s"
		task.wait(1)
	end
	if gui.Parent then gui:Destroy() end
end)
