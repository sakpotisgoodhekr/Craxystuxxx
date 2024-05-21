-- Service to get players
local Players = game:GetService("Players")

-- Function to handle player attacks
local function onPlayerAttack(player, target)
    if target and target:IsA("Humanoid") then
        -- Deal lethal damage
        target:TakeDamage(target.MaxHealth)
    end
end

-- Function to set up weapons for a player
local function setupWeapons(player)
    -- Wait for the player's character to be added
    player.CharacterAdded:Connect(function(character)
        -- Wait for the character to fully load
        wait(1)
        
        -- Get the player's backpack to access tools
        local backpack = player:FindFirstChildOfClass("Backpack")
        if backpack then
            for _, tool in pairs(backpack:GetChildren()) do
                if tool:IsA("Tool") then
                    -- Connect the tool's Activated event to handle attacks
                    tool.Activated:Connect(function()
                        -- Check if the tool has a hitbox
                        local handle = tool:FindFirstChild("Handle")
                        if handle then
                            -- Detect hits on other players
                            handle.Touched:Connect(function(hit)
                                local hitPlayer = Players:GetPlayerFromCharacter(hit.Parent)
                                if hitPlayer and hitPlayer ~= player then
                                    -- Get the humanoid of the hit player
                                    local humanoid = hit.Parent:FindFirstChildOfClass("Humanoid")
                                    if humanoid then
                                        onPlayerAttack(player, humanoid)
                                    end
                                end
                            end)
                        end
                    end)
                end
            end
        end
    end)
end

-- Set up weapons for all existing players
for _, player in pairs(Players:GetPlayers()) do
    setupWeapons(player)
end

-- Set up weapons for new players
Players.PlayerAdded:Connect(setupWeapons)
