-- ✅ JHDH Ai Aim Assist Script
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

-- رسالة ترحيب
local msg = Instance.new("Message", workspace)
msg.Text = "✅ JHDH Ai Aim Assist Script ✅"
task.delay(3, function() msg:Destroy() end)

-- إعدادات
local Settings = {
    AimbotEnabled = true,
    ESPEnabled = true,
    TracerEnabled = false,
    FOVSize = 100,
    AimPart = "Head",
    AimKey = Enum.UserInputType.MouseButton2,
    Smoothing = 0.2
}

-- واجهة GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 250, 0, 200)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0

-- عنوان السكربت
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "🎯 Aim Assist v1 - JHDH"
title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18

-- وظيفة إضافة زر تبديل
local function addToggle(name, default, callback)
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, #frame:GetChildren() * 35)
    btn.Text = name .. ": " .. tostring(default)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.MouseButton1Click:Connect(function()
        default = not default
        btn.Text = name .. ": " .. tostring(default)
        callback(default)
    end)
end

-- زر تغيير حجم FOV
local function addFOVSlider()
    local label = Instance.new("TextButton", frame)
    label.Size = UDim2.new(1, -20, 0, 30)
    label.Position = UDim2.new(0, 10, 0, #frame:GetChildren() * 35)
    label.Text = "FOV Size: " .. Settings.FOVSize
    label.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    label.TextColor3 = Color3.new(1, 1, 1)

    local sizes = {60, 100, 200, 300}
    local index = 2
    label.MouseButton1Click:Connect(function()
        index = index % #sizes + 1
        Settings.FOVSize = sizes[index]
        label.Text = "FOV Size: " .. Settings.FOVSize
        fovCircle.Radius = Settings.FOVSize
    end)
end

-- إضافة الخيارات
addToggle("Aimbot", Settings.AimbotEnabled, function(v) Settings.AimbotEnabled = v end)
addToggle("ESP", Settings.ESPEnabled, function(v) Settings.ESPEnabled = v end)
addToggle("Tracer", Settings.TracerEnabled, function(v) Settings.TracerEnabled = v end)
addFOVSlider()

-- دائرة FOV
local fovCircle = Drawing.new("Circle")
fovCircle.Color = Color3.fromRGB(0, 255, 0)
fovCircle.Thickness = 1
fovCircle.Filled = false
fovCircle.Transparency = 0.5
fovCircle.Radius = Settings.FOVSize

RunService.RenderStepped:Connect(function()
    local mousePos = UserInputService:GetMouseLocation()
    fovCircle.Position = Vector2.new(mousePos.X, mousePos.Y)
end)

-- ESP و Tracer
local espObjects = {}

local function createESP(player)
    local box = Drawing.new("Square")
    box.Color = Color3.fromRGB(255, 0, 0)
    box.Thickness = 1
    box.Filled = false
    box.Transparency = 1

    local nameTag = Drawing.new("Text")
    nameTag.Color = Color3.fromRGB(255, 255, 255)
    nameTag.Size = 14
    nameTag.Center = true
    nameTag.Outline = true

    local tracer = Drawing.new("Line")
    tracer.Color = Color3.fromRGB(0, 255, 255)
    tracer.Thickness = 1
    tracer.Transparency = 1

    espObjects[player] = {box = box, nameTag = nameTag, tracer = tracer}
end

local function removeESP(player)
    if espObjects[player] then
        for _, obj in pairs(espObjects[player]) do obj:Remove() end
        espObjects[player] = nil
    end
end

for _, player in pairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then createESP(player) end
end

Players.PlayerAdded:Connect(function(player)
    if player ~= LocalPlayer then createESP(player) end
end)

Players.PlayerRemoving:Connect(removeESP)

RunService.RenderStepped:Connect(function()
    for player, drawings in pairs(espObjects) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local pos, onScreen = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if Settings.ESPEnabled and onScreen then
                local size = 50
                drawings.box.Size = Vector2.new(size, size * 2)
                drawings.box.Position = Vector2.new(pos.X - size / 2, pos.Y - size)
                drawings.box.Visible = true

                drawings.nameTag.Position = Vector2.new(pos.X, pos.Y - size - 15)
                drawings.nameTag.Text = player.Name
                drawings.nameTag.Visible = true
            else
                drawings.box.Visible = false
                drawings.nameTag.Visible = false
            end

            if Settings.TracerEnabled and onScreen then
                drawings.tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                drawings.tracer.To = Vector2.new(pos.X, pos.Y)
                drawings.tracer.Visible = true
            else
                drawings.tracer.Visible = false
            end
        else
            drawings.box.Visible = false
            drawings.nameTag.Visible = false
            drawings.tracer.Visible = false
        end
    end
end)

-- Aimbot
local function getClosestTarget()
    local closest = nil
    local shortestDistance = Settings.FOVSize
    local mousePos = UserInputService:GetMouseLocation()

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(Settings.AimPart) then
            local part = player.Character[Settings.AimPart]
            local screenPos, onScreen = Camera:WorldToViewportPoint(part.Position)
            if onScreen then
                local distance = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
                if distance < shortestDistance then
                    shortestDistance = distance
                    closest = part
                end
            end
        end
    end

    return closest
end

RunService.RenderStepped:Connect(function()
    if Settings.AimbotEnabled and UserInputService:IsMouseButtonPressed(Settings.AimKey) then
        local target = getClosestTarget()
        if target then
            local direction = (target.Position - Camera.CFrame.Position).Unit
            local newCFrame = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + direction)
            Camera.CFrame = Camera.CFrame:Lerp(newCFrame, Settings.Smoothing)
        end
    end
end)
