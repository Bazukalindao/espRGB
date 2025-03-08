local function createESP(player)
    local character = player.Character
    if not character then return end

    local head = character:WaitForChild("Head")

    local billboard = Instance.new("BillboardGui")
    billboard.Adornee = head
    billboard.Size = UDim2.new(2, 0, 2, 0)
    billboard.StudsOffset = Vector3.new(0, 2, 0)
    billboard.AlwaysOnTop = true

    local textLabel = Instance.new("TextLabel")
    textLabel.Text = player.Name
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.TextColor3 = Color3.new(1, 1, 1)
    textLabel.BackgroundTransparency = 1
    textLabel.Parent = billboard

    billboard.Parent = head

    local highlight = Instance.new("Highlight")
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.FillColor = Color3.new(0, 1, 0)
    highlight.OutlineColor = Color3.new(1, 1, 1)
    highlight.Parent = character
end

local function removeESP(player)
    local character = player.Character
    if not character then return end

    local head = character:FindFirstChild("Head")
    if not head then return end

    for _, child in pairs(head:GetChildren()) do
        if child:IsA("BillboardGui") then
            child:Destroy()
        end
    end

    for _, child in pairs(character:GetChildren()) do
        if child:IsA("Highlight") then
            child:Destroy()
        end
    end
end

local function trackPlayer(player)
    player.CharacterAdded:Connect(function()
        createESP(player)
    end)

    player.CharacterRemoving:Connect(function()
        removeESP(player)
    end)

    if player.Character then
        createESP(player)
    end
end

game.Players.PlayerAdded:Connect(function(player)
    trackPlayer(player)
end)

game.Players.PlayerRemoving:Connect(function(player)
    removeESP(player)
end)

for _, player in pairs(game.Players:GetPlayers()) do
    if player ~= game.Players.LocalPlayer then
        trackPlayer(player)
    end
end
