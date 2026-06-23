--[[
    Script: Modern GUI Hub for Roblox
    Description: A complete script for a modern, minimalistic GUI with ESP, Infinite Jump, and Speed control.
    Features: Draggable window, minimize/maximize, dark theme, smooth animations, and responsive design.
]]

-- Services
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

-- Local Player
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Variables for features
local espEnabled = false
local infiniteJumpEnabled = false
local espObjects = {}
local currentSpeed = 16 -- Default speed

-- GUI Creation
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ModernHubGUI"
screenGui.Parent = player.PlayerGui
screenGui.ResetOnSpawn = false

-- Main Frame (Window)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Parent = screenGui
mainFrame.Size = UDim2.new(0, 400, 0, 500)
mainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
mainFrame.BackgroundTransparency = 0.95
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true

-- Corners
local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 12)
mainCorner.Parent = mainFrame

-- Shadow effect (simple)
local shadow = Instance.new("Frame")
shadow.Name = "Shadow"
shadow.Parent = mainFrame
shadow.Size = UDim2.new(1, 0, 1, 0)
shadow.Position = UDim2.new(0, 0, 0, 0)
shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
shadow.BackgroundTransparency = 0.7
shadow.BorderSizePixel = 0
shadow.ZIndex = 0

local shadowCorner = Instance.new("UICorner")
shadowCorner.CornerRadius = UDim.new(0, 12)
shadowCorner.Parent = shadow

-- Title Bar (for dragging)
local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Parent = mainFrame
titleBar.Size = UDim2.new(1, 0, 0, 40)
titleBar.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
titleBar.BackgroundTransparency = 0.3
titleBar.BorderSizePixel = 0

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 12)
titleCorner.Parent = titleBar

-- Title Text
local titleText = Instance.new("TextLabel")
titleText.Name = "TitleText"
titleText.Parent = titleBar
titleText.Size = UDim2.new(1, -60, 1, 0)
titleText.Position = UDim2.new(0, 20, 0, 0)
titleText.BackgroundTransparency = 1
titleText.Text = "⚡ HUB CONTROLS"
titleText.TextColor3 = Color3.fromRGB(220, 220, 230)
titleText.TextSize = 18
titleText.TextXAlignment = Enum.TextXAlignment.Left
titleText.Font = Enum.Font.GothamBold

-- Minimize/Maximize Button
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Parent = titleBar
toggleButton.Size = UDim2.new(0, 30, 0, 30)
toggleButton.Position = UDim2.new(1, -40, 0, 5)
toggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
toggleButton.BackgroundTransparency = 0.5
toggleButton.Text = "−"
toggleButton.TextColor3 = Color3.fromRGB(200, 200, 210)
toggleButton.TextSize = 22
toggleButton.Font = Enum.Font.GothamBold
toggleButton.BorderSizePixel = 0

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 6)
toggleCorner.Parent = toggleButton

-- Close Button
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Parent = titleBar
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -80, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeButton.BackgroundTransparency = 0.3
closeButton.Text = "✕"
closeButton.TextColor3 = Color3.fromRGB(220, 220, 230)
closeButton.TextSize = 18
closeButton.Font = Enum.Font.GothamBold
closeButton.BorderSizePixel = 0

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = closeButton

-- Content Frame
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Parent = mainFrame
contentFrame.Size = UDim2.new(1, -40, 1, -80)
contentFrame.Position = UDim2.new(0, 20, 0, 60)
contentFrame.BackgroundTransparency = 1

-- ESP Button
local espButton = Instance.new("TextButton")
espButton.Name = "ESPButton"
espButton.Parent = contentFrame
espButton.Size = UDim2.new(1, 0, 0, 50)
espButton.Position = UDim2.new(0, 0, 0, 0)
espButton.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
espButton.BackgroundTransparency = 0.5
espButton.Text = "🎯 ESP: OFF"
espButton.TextColor3 = Color3.fromRGB(200, 200, 210)
espButton.TextSize = 16
espButton.Font = Enum.Font.GothamBold
espButton.BorderSizePixel = 0

local espCorner = Instance.new("UICorner")
espCorner.CornerRadius = UDim.new(0, 8)
espCorner.Parent = espButton

-- Infinite Jump Button
local jumpButton = Instance.new("TextButton")
jumpButton.Name = "JumpButton"
jumpButton.Parent = contentFrame
jumpButton.Size = UDim2.new(1, 0, 0, 50)
jumpButton.Position = UDim2.new(0, 0, 0, 65)
jumpButton.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
jumpButton.BackgroundTransparency = 0.5
jumpButton.Text = "🦘 Infinite Jump: OFF"
jumpButton.TextColor3 = Color3.fromRGB(200, 200, 210)
jumpButton.TextSize = 16
jumpButton.Font = Enum.Font.GothamBold
jumpButton.BorderSizePixel = 0

local jumpCorner = Instance.new("UICorner")
jumpCorner.CornerRadius = UDim.new(0, 8)
jumpCorner.Parent = jumpButton

-- Speed Label
local speedLabel = Instance.new("TextLabel")
speedLabel.Name = "SpeedLabel"
speedLabel.Parent = contentFrame
speedLabel.Size = UDim2.new(1, 0, 0, 30)
speedLabel.Position = UDim2.new(0, 0, 0, 130)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "Speed (WalkSpeed):"
speedLabel.TextColor3 = Color3.fromRGB(180, 180, 190)
speedLabel.TextSize = 14
speedLabel.TextXAlignment = Enum.TextXAlignment.Left
speedLabel.Font = Enum.Font.Gotham

-- Speed TextBox
local speedBox = Instance.new("TextBox")
speedBox.Name = "SpeedBox"
speedBox.Parent = contentFrame
speedBox.Size = UDim2.new(1, 0, 0, 45)
speedBox.Position = UDim2.new(0, 0, 0, 165)
speedBox.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
speedBox.BackgroundTransparency = 0.5
speedBox.Text = tostring(currentSpeed)
speedBox.TextColor3 = Color3.fromRGB(220, 220, 230)
speedBox.TextSize = 18
speedBox.Font = Enum.Font.GothamBold
speedBox.PlaceholderText = "Enter speed..."
speedBox.BorderSizePixel = 0

local speedCorner = Instance.new("UICorner")
speedCorner.CornerRadius = UDim.new(0, 8)
speedCorner.Parent = speedBox

-- Status Label
local statusLabel = Instance.new("TextLabel")
statusLabel.Name = "StatusLabel"
statusLabel.Parent = contentFrame
statusLabel.Size = UDim2.new(1, 0, 0, 30)
statusLabel.Position = UDim2.new(0, 0, 0, 225)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "Status: Ready"
statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
statusLabel.TextSize = 13
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.Font = Enum.Font.Gotham

-- Dragging Logic
local dragging = false
local dragStart
local startPos

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Minimize/Maximize Functionality
local minimized = false
local mainSize = mainFrame.Size

toggleButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    local targetSize = minimized and UDim2.new(0, 400, 0, 40) or mainSize
    local targetTransparency = minimized and 0.98 or 0.95
    
    local tween = TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Size = targetSize,
        BackgroundTransparency = targetTransparency
    })
    tween:Play()
    
    toggleButton.Text = minimized and "+" or "−"
end)

-- Close Button (hides GUI)
closeButton.MouseButton1Click:Connect(function()
    local tween = TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0),
        Position = UDim2.new(0.5, 0, 0.5, 0)
    })
    tween:Play()
    tween.Completed:Connect(function()
        screenGui.Enabled = false
    end)
end)

-- Reopen GUI (press Shift + H)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.H and UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
        if not screenGui.Enabled then
            screenGui.Enabled = true
            mainFrame.Size = UDim2.new(0, 0, 0, 0)
            mainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
            
            local tween = TweenService:Create(mainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                Size = mainSize,
                Position = UDim2.new(0.5, -200, 0.5, -250)
            })
            tween:Play()
        end
    end
end)

-- Hover Effects for Buttons
local function applyHoverEffect(button, hoverColor, normalColor)
    button.MouseEnter:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
            BackgroundColor3 = hoverColor,
            BackgroundTransparency = 0.3
        }):Play()
    end)
    
    button.MouseLeave:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
            BackgroundColor3 = normalColor,
            BackgroundTransparency = 0.5
        }):Play()
    end)
end

applyHoverEffect(espButton, Color3.fromRGB(70, 70, 90), Color3.fromRGB(45, 45, 55))
applyHoverEffect(jumpButton, Color3.fromRGB(70, 70, 90), Color3.fromRGB(45, 45, 55))
applyHoverEffect(toggleButton, Color3.fromRGB(80, 80, 100), Color3.fromRGB(60, 60, 70))
applyHoverEffect(closeButton, Color3.fromRGB(220, 60, 60), Color3.fromRGB(200, 50, 50))

-- ESP Functionality
local function createESP(playerToESP)
    if not playerToESP.Character then return end
    local highlight = Instance.new("Highlight")
    highlight.Name = "ESP_Highlight"
    highlight.Parent = playerToESP.Character
    highlight.Adornee = playerToESP.Character
    highlight.FillColor = Color3.fromRGB(0, 200, 255)
    highlight.FillTransparency = 0.3
    highlight.OutlineColor = Color3.fromRGB(0, 255, 255)
    highlight.OutlineTransparency = 0.1
    table.insert(espObjects, highlight)
end

local function clearESP()
    for _, obj in ipairs(espObjects) do
        if obj and obj.Parent then
            obj:Destroy()
        end
    end
    espObjects = {}
end

local function toggleESP()
    espEnabled = not espEnabled
    espButton.Text = espEnabled and "🎯 ESP: ON" or "🎯 ESP: OFF"
    espButton.TextColor3 = espEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(200, 200, 210)
    
    if espEnabled then
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= player then
                createESP(plr)
            end
        end
        statusLabel.Text = "Status: ESP Active"
        statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    else
        clearESP()
        statusLabel.Text = "Status: ESP Disabled"
        statusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    end
end

espButton.MouseButton1Click:Connect(toggleESP)

-- ESP for new players
Players.PlayerAdded:Connect(function(plr)
    if espEnabled then
        plr.CharacterAdded:Connect(function()
            if espEnabled and plr ~= player then
                createESP(plr)
            end
        end)
    end
end)

-- Infinite Jump Functionality
local function toggleInfiniteJump()
    infiniteJumpEnabled = not infiniteJumpEnabled
    jumpButton.Text = infiniteJumpEnabled and "🦘 Infinite Jump: ON" or "🦘 Infinite Jump: OFF"
    jumpButton.TextColor3 = infiniteJumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(200, 200, 210)
    
    statusLabel.Text = infiniteJumpEnabled and "Status: Infinite Jump ON" or "Status: Infinite Jump OFF"
    statusLabel.TextColor3 = infiniteJumpEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
end

jumpButton.MouseButton1Click:Connect(toggleInfiniteJump)

-- Infinite Jump Logic
local jumpConnection
RunService.Heartbeat:Connect(function()
    if infiniteJumpEnabled and humanoid and humanoid.Parent then
        if humanoid:GetState() == Enum.HumanoidStateType.Jumping or 
           humanoid:GetState() == Enum.HumanoidStateType.Freefall then
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)

-- Speed Control
speedBox.FocusLost:Connect(function(enterPressed)
    if not enterPressed then return end
    local speedValue = tonumber(speedBox.Text)
    if speedValue and speedValue > 0 then
        currentSpeed = speedValue
        if humanoid then
            humanoid.WalkSpeed = currentSpeed
        end
        statusLabel.Text = "Status: Speed set to " .. currentSpeed
        statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
        speedBox.Text = tostring(currentSpeed)
    else
        speedBox.Text = tostring(currentSpeed)
        statusLabel.Text = "Status: Invalid speed value!"
        statusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        task.wait(1)
        statusLabel.Text = "Status: Ready"
        statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
    end
end)

-- Update speed when character respawns
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoid = character:WaitForChild("Humanoid")
    task.wait(0.5)
    humanoid.WalkSpeed = currentSpeed
end)

-- Initial Speed Setup
task.wait(1)
if humanoid then
    humanoid.WalkSpeed = currentSpeed
end

-- Cleanup on player leave
player.CharacterRemoving:Connect(function()
    clearESP()
end)

-- Status display for first load
statusLabel.Text = "Status: Ready - Press Shift+H to toggle GUI"
statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)

print("✅ Modern GUI Hub loaded successfully!")
print("🔄 Press Shift+H to show/hide the GUI")
