local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local ESPEnabled = false
local ESPTransparency = 0.5
local ESPUpdateDelay = 0.1

local ESPBoxColor = Color3.fromRGB(255, 0, 0)
local ESPTextColor = Color3.fromRGB(255, 255, 255)

local function getRoot(model)
    if model:IsA("Model") then
        return model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart")
    end
    return model
end

local function createESP(plr)
    if not plr.Character then return end
    local character = plr.Character
    local head = character:FindFirstChild("Head")
    local rootPart = getRoot(character)

    if not rootPart or not head then return end

    local espFolder = CoreGui:FindFirstChild(plr.Name .. "_ESP")
    if espFolder then
        espFolder:Destroy()
    end

    espFolder = Instance.new("Folder")
    espFolder.Name = plr.Name .. "_ESP"
    espFolder.Parent = CoreGui

    local function createBox(part)
        local box = Instance.new("BoxHandleAdornment")
        box.Name = "ESPBox"
        box.Adornee = part
        box.AlwaysOnTop = true
        box.ZIndex = 10
        box.Size = part.Size
        box.Transparency = ESPTransparency
        box.Color3 = ESPBoxColor
        box.Parent = espFolder
    end

    for _, part in ipairs(character:GetChildren()) do
        if part:IsA("BasePart") then
            createBox(part)
        end
    end

    local billboardGui = Instance.new("BillboardGui")
    billboardGui.Adornee = head
    billboardGui.Size = UDim2.new(0, 150, 0, 75)
    billboardGui.StudsOffset = Vector3.new(0, 3, 0)
    billboardGui.AlwaysOnTop = true
    billboardGui.Parent = espFolder

    local textLabel = Instance.new("TextLabel")
    textLabel.Parent = billboardGui
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Font = Enum.Font.GothamBold
    textLabel.TextSize = 18
    textLabel.TextColor3 = ESPTextColor
    textLabel.TextStrokeTransparency = 0.7
    textLabel.Text = plr.Name

    local function updateESP()
        if not character or not rootPart then return end
        if Players.LocalPlayer.Character then
            local localRoot = getRoot(Players.LocalPlayer.Character)
            if localRoot then
                local distance = (localRoot.Position - rootPart.Position).Magnitude
                textLabel.Text = plr.Name .. " | " .. math.floor(distance) .. " studs"
            end
        end
    end

    local espLoop
    espLoop = RunService.RenderStepped:Connect(updateESP)

    plr.CharacterRemoving:Connect(function()
        espFolder:Destroy()
        espLoop:Disconnect()
    end)
end

local function toggleESP()
    ESPEnabled = not ESPEnabled
    for _, plr in ipairs(Players:GetPlayers()) do
        if ESPEnabled then
            createESP(plr)
        else
            local espFolder = CoreGui:FindFirstChild(plr.Name .. "_ESP")
            if espFolder then espFolder:Destroy() end
        end
    end
end

Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        if ESPEnabled then
            createESP(plr)
        end
    end)
end)

-- GUI

local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer.PlayerGui
screenGui.Name = "BAZUKA_HUB"

local frame = Instance.new("Frame")
frame.Parent = screenGui
frame.Size = UDim2.new(0, 250, 0, 150)
frame.Position = UDim2.new(0, 10, 0, 10)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.BackgroundTransparency = 0.5
frame.BorderSizePixel = 0

local titleLabel = Instance.new("TextLabel")
titleLabel.Parent = frame
titleLabel.Size = UDim2.new(1, 0, 0, 25)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "BAZUKA HUB"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextSize = 20
titleLabel.Font = Enum.Font.GothamBold

local espButton = Instance.new("TextButton")
espButton.Parent = frame
espButton.Size = UDim2.new(0, 230, 0, 30)
espButton.Position = UDim2.new(0, 10, 0, 50)
espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
espButton.Text = "Toggle ESP"
espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
espButton.TextSize = 16
espButton.Font = Enum.Font.Gotham

-- Adicionando o ícone do pinguim
local icon = Instance.new("ImageLabel")
icon.Parent = frame
icon.Size = UDim2.new(0, 50, 0, 50)
icon.Position = UDim2.new(0, 10, 0, 90)
icon.Image = "https://i.postimg.cc/k4QxrytK/file-4dt-AGRnh-BZJY3t-Af-My-M2-Zf.webp"

espButton.MouseButton1Click:Connect(function()
    toggleESP()
    if ESPEnabled then
        espButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        espButton.Text = "Disable ESP"
    else
        espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        espButton.Text = "Toggle ESP"
    end
end)

screenGui.Enabled = true
