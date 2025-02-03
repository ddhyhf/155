local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local StarterGui = game:GetService("StarterGui")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

local flying = false
local flySpeed = 100
local bodyVelocity, bodyGyro
local isMobile = false
local flyButton

-- 創建 UI 選擇裝置
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0.3, 0, 0.2, 0)
frame.Position = UDim2.new(0.35, 0, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
frame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0.3, 0)
title.Text = "選擇裝置"
title.TextScaled = true
title.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
title.Parent = frame

local pcButton = Instance.new("TextButton")
pcButton.Size = UDim2.new(0.5, 0, 0.5, 0)
pcButton.Position = UDim2.new(0, 0, 0.5, 0)
pcButton.Text = "電腦"
pcButton.TextScaled = true
pcButton.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
pcButton.Parent = frame

local mobileButton = Instance.new("TextButton")
mobileButton.Size = UDim2.new(0.5, 0, 0.5, 0)
mobileButton.Position = UDim2.new(0.5, 0, 0.5, 0)
mobileButton.Text = "手機"
mobileButton.TextScaled = true
mobileButton.BackgroundColor3 = Color3.fromRGB(0, 255, 150)
mobileButton.Parent = frame

-- 飛行功能
local function startFlying()
    if flying then return end
    flying = true

    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyVelocity.Parent = humanoidRootPart

    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.CFrame = humanoidRootPart.CFrame
    bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    bodyGyro.Parent = humanoidRootPart
end

local function stopFlying()
    if not flying then return end
    flying = false

    if bodyVelocity then bodyVelocity:Destroy() end
    if bodyGyro then bodyGyro:Destroy() end
end

RunService.RenderStepped:Connect(function()
    if flying then
        local camera = Workspace.CurrentCamera
        local camLook = camera.CFrame.LookVector
        bodyVelocity.Velocity = camLook * flySpeed
        bodyGyro.CFrame = CFrame.new(humanoidRootPart.Position, humanoidRootPart.Position + camLook)
    end
end)

local function createMobileUI()
    flyButton = Instance.new("TextButton")
    flyButton.Size = UDim2.new(0.15, 0, 0.1, 0)
    flyButton.Position = UDim2.new(0.8, 0, 0.05, 0)
    flyButton.Text = "飛行"
    flyButton.TextScaled = true
    flyButton.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    flyButton.Parent = screenGui

    flyButton.MouseButton1Click:Connect(function()
        if flying then
            stopFlying()
            flyButton.Text = "飛行"
        else
            startFlying()
            flyButton.Text = "停止"
        end
    end)
end

pcButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

mobileButton.MouseButton1Click:Connect(function()
    isMobile = true
    createMobileUI()
    screenGui:Destroy()
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed or isMobile then return end
    if input.KeyCode == Enum.KeyCode.V then
        if flying then
            stopFlying()
        else
            startFlying()
        end
    end
end)
