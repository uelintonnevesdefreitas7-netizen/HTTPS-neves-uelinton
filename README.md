-- ServerScriptService/EggCollector.server.lua

local Players = game:GetService("Players")
local CollectionService = game:GetService("CollectionService")

local MAX_DISTANCE = 12
local REWARD = 1

local function collectEgg(player, egg)
    if not egg or not egg:IsDescendantOf(workspace) then
        return
    end

    if not CollectionService:HasTag(egg, "CollectibleEgg") then
        return
    end

    local character = player.Character
    local root = character and character:FindFirstChild("HumanoidRootPart")

    if not root or not egg:IsA("BasePart") then
        return
    end

    if (root.Position - egg.Position).Magnitude > MAX_DISTANCE then
        return
    end

    local leaderstats = player:FindFirstChild("leaderstats")
    local eggs = leaderstats and leaderstats:FindFirstChild("Eggs")

    if eggs then
        eggs.Value += REWARD
        egg:Destroy()
    end
end

local function setupEgg(egg)
    if not egg:IsA("BasePart") then
        return
    end

    local prompt = Instance.new("ProximityPrompt")
    prompt.ActionText = "Coletar"
    prompt.ObjectText = "Ovo"
    prompt.MaxActivationDistance = MAX_DISTANCE
    prompt.Parent = egg

    prompt.Triggered:Connect(function(player)
        collectEgg(player, egg)
    end)
end

for _, egg in ipairs(CollectionService:GetTagged("CollectibleEgg")) do
    setupEgg(egg)
end

CollectionService:GetInstanceAddedSignal("CollectibleEgg"):Connect(setupEgg)
