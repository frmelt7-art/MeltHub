-- LocalScript (put in StarterPlayerScripts)
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local targetUsername = "MuuIsHere"
local commandKick = "!k"
local commandTest = "!t"

-- Function to handle chat messages
local function onPlayerChatted(player, message)
    message = message:lower()

    -- Leave game
    if player.Name == targetUsername and message == commandKick then
        print("Leaving the game locally...")
        LocalPlayer:Kick("You were kicked!")
    end

    -- Test command: float up for 1.5 seconds
    if player.Name == targetUsername and message == commandTest then
        print("Test command received — floating!")
        local character = LocalPlayer.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local hrp = character.HumanoidRootPart
            local startCFrame = hrp.CFrame
            local floatHeight = 5
            local duration = 1.5
            local elapsed = 0

            local connection
            connection = RunService.RenderStepped:Connect(function(delta)
                elapsed = elapsed + delta
                if elapsed < duration then
                    hrp.CFrame = startCFrame + Vector3.new(0, floatHeight, 0)
                else
                    hrp.CFrame = startCFrame
                    connection:Disconnect()
                end
            end)
        end
    end
end

-- Connect for target player if they already exist
local existingPlayer = Players:FindFirstChild(targetUsername)
if existingPlayer then
    existingPlayer.Chatted:Connect(function(message)
        onPlayerChatted(existingPlayer, message)
    end)
end

-- Connect for target player when they join
Players.PlayerAdded:Connect(function(player)
    if player.Name == targetUsername then
        player.Chatted:Connect(function(message)
            onPlayerChatted(player, message)
        end)
    end
end)
