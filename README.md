-- Create the main GUI
local DaviHub = Instance.new("ScreenGui")
DaviHub.Name = "DaviHub/Kat"
DaviHub.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 200, 0, 160) -- Reduced height
MainFrame.Position = UDim2.new(0.5, -100, 0.5, -80) -- Adjusted position
MainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = DaviHub

local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Text = "DaviHub/Kat"
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
Title.Parent = MainFrame

-- Button for the aimbot functionality
local AimbotButton = Instance.new("TextButton")
AimbotButton.Name = "AimbotButton"
AimbotButton.Text = "Aimbot"
AimbotButton.Size = UDim2.new(0.9, 0, 0, 30)
AimbotButton.Position = UDim2.new(0.05, 0, 0.2, 0)
AimbotButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
AimbotButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AimbotButton.Font = Enum.Font.Gotham
AimbotButton.TextSize = 12
AimbotButton.Parent = MainFrame

-- Button for the gun mod functionality
local GunModButton = Instance.new("TextButton")
GunModButton.Name = "GunModButton"
GunModButton.Text = "Gun Mod"
GunModButton.Size = UDim2.new(0.9, 0, 0, 30)
GunModButton.Position = UDim2.new(0.05, 0, 0.5, 0)
GunModButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
GunModButton.TextColor3 = Color3.fromRGB(255, 255, 255)
GunModButton.Font = Enum.Font.Gotham
GunModButton.TextSize = 12
GunModButton.Parent = MainFrame

-- Button for ESP functionality
local ESPButton = Instance.new("TextButton")
ESPButton.Name = "ESPButton"
ESPButton.Text = "ESP"
ESPButton.Size = UDim2.new(0.9, 0, 0, 30)
ESPButton.Position = UDim2.new(0.05, 0, 0.8, 0)
ESPButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
ESPButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ESPButton.Font = Enum.Font.Gotham
ESPButton.TextSize = 12
ESPButton.Parent = MainFrame

-- Variables to track toggle states
local aimbotEnabled = false
local gunModEnabled = false
local espEnabled = false
local espColor = Color3.fromRGB(255, 255, 255)

-- Aimbot functionality
local function setupAimbot()
    local localPlayer = game:GetService("Players").LocalPlayer
    local currentCamera = game:GetService("Workspace").CurrentCamera
    local mouse = localPlayer:GetMouse()

    local function getClosestPlayerToCursor(x, y)
        local closestPlayer = nil
        local shortestDistance = math.huge

        for i, v in pairs(game:GetService("Players"):GetPlayers()) do
            if v ~= localPlayer and v.Character and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid.Health ~= 0 and v.Character:FindFirstChild("HumanoidRootPart") and v.Character:FindFirstChild("Head") then
                local pos = currentCamera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position)
                local magnitude = (Vector2.new(pos.X, pos.Y) - Vector2.new(x, y)).magnitude

                if magnitude < shortestDistance then
                    closestPlayer = v
                    shortestDistance = magnitude
                end
            end
        end

        return closestPlayer
    end

    local mt = getrawmetatable(game)
    local oldIndex = mt.__index
    if setreadonly then setreadonly(mt, false) else make_writeable(mt, true) end
    local newClose = newcclosure or function(f) return f end

    mt.__index = newClose(function(t, k)
        if not checkcaller() and aimbotEnabled and t == mouse and tostring(k) == "X" and string.find(getfenv(2).script.Name, "Client") and getClosestPlayerToCursor() then
            local closest = getClosestPlayerToCursor(oldIndex(t, k), oldIndex(t, "Y")).Character.Head
            local pos = currentCamera:WorldToScreenPoint(closest.Position)
            return pos.X
        end
        if not checkcaller() and aimbotEnabled and t == mouse and tostring(k) == "Y" and string.find(getfenv(2).script.Name, "Client") and getClosestPlayerToCursor() then
            local closest = getClosestPlayerToCursor(oldIndex(t, "X"), oldIndex(t, k)).Character.Head
            local pos = currentCamera:WorldToScreenPoint(closest.Position)
            return pos.Y
        end
        if aimbotEnabled and t == mouse and tostring(k) == "Hit" and string.find(getfenv(2).script.Name, "Client") and getClosestPlayerToCursor() then
            return getClosestPlayerToCursor(mouse.X, mouse.Y).Character.Head.CFrame
        end

        return oldIndex(t, k)
    end)

    if setreadonly then setreadonly(mt, true) else make_writeable(mt, false) end
end

-- Gun mod functionality
local function setupGunMod()
    spawn(function()
        while wait(1) do
            if gunModEnabled then
                for i, v in next, getgc(true) do
                    if type(v) == "table" then
                        if rawget(v, "LoadedAmmo") then
                            v.LoadedAmmo = 10000000000
                            v.RecoilFactor = 0
                            v.Spread = 0
                        end
                        if rawget(v, "ReloadTime") then
                            v.ReloadTime = 0
                            v.EquipTime = 0
                            v.LoadCapacity = 10000000000
                        end
                    end
                end
            end
        end
    end)
end

-- ESP functionality
local function setupESP()
    if espEnabled == false then
        for _,v in pairs(game.Workspace:GetChildren()) do
            if v:FindFirstChildWhichIsA("Humanoid") then
                if v:FindFirstChildWhichIsA("Highlight") then
                    v:FindFirstChildWhichIsA("Highlight"):Destroy()
                end
            end
        end
        return
    end
    
    game:GetService("RunService").Heartbeat:Connect(function()
        if espEnabled then
            for _,v in pairs(game.Workspace:GetChildren()) do
                if v:FindFirstChildWhichIsA("Humanoid") then
                    if not v:FindFirstChildWhichIsA("Highlight") then
                        local h = Instance.new("Highlight",v)
                        h.Name = "penis"
                        h.FillTransparency = 0.1
                        h.OutlineTransparency = 1
                        h.FillColor = espColor
                    else
                        v:FindFirstChildWhichIsA("Highlight").FillColor = espColor
                    end
                end
            end
        end
    end)
end

-- Button click handlers
AimbotButton.MouseButton1Click:Connect(function()
    aimbotEnabled = not aimbotEnabled
    AimbotButton.BackgroundColor3 = aimbotEnabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
    
    if aimbotEnabled then
        setupAimbot()
    end
end)

GunModButton.MouseButton1Click:Connect(function()
    gunModEnabled = not gunModEnabled
    GunModButton.BackgroundColor3 = gunModEnabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
    
    if gunModEnabled then
        setupGunMod()
    end
end)

ESPButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    ESPButton.BackgroundColor3 = espEnabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)
    
    setupESP()
end)

-- Make the GUI draggable
local UserInputService = game:GetService("UserInputService")
local dragging
local dragInput
local dragStart
local startPos

local function update(input)
    local delta = input.Position - dragStart
    MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

Title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

Title.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)
