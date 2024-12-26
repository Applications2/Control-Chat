-- OrionLib Script Setup
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Orion/main/source"))()

-- Main UI Window
local Window = OrionLib:MakeWindow({
    Name = "Control Chat | Official Chat Scripts",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "ControlChatScripts"
})

-- Instructions Paragraph
OrionLib:MakeNotification({
    Name = "How to Be Hidden",
    Content = [[
How to Be Hidden:
1. Remove your accessories.
2. Remove your hair.
3. Look for a headless avatar to be more effectively hidden. 🫥

How to Use the Script:
1. Try using this script in Brookhaven for testing.
2. Make sure to click "Remove Face" before targeting a player.
3. Enjoy your trolling fun!
    ]],
    Image = "rbxassetid://4483345998",
    Time = 10
})

-- Variables
local TargetDisplayName = ""

-- Helper function to find player by display name
local function GetPlayerByDisplayName(displayName)
    for _, player in pairs(game:GetService("Players"):GetPlayers()) do
        if player.DisplayName == displayName then
            return player
        end
    end
    return nil
end

-- Helper function to shrink the player's character
local function ShrinkCharacter(character)
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("BasePart") then
            part.Size = Vector3.new(0.05, 0.05, 0.05) -- Microscopic size
            part.Transparency = 1 -- Fully invisible
        elseif part:IsA("Humanoid") then
            part:ChangeState(Enum.HumanoidStateType.Physics)
        end
    end
end

-- Function to remove face from the player's character
local function RemoveFace(character)
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("Decal") and part.Name == "face" then
            part:Destroy() -- Remove face decal
        end
    end
    OrionLib:MakeNotification({
        Name = "Face Removed",
        Content = "Your face has been removed to enhance hiding!",
        Image = "rbxassetid://4483345998",
        Time = 3
    })
end

-- Tab for Head Sit and Chat Monitor
local MainTab = Window:MakeTab({
    Name = "Head Sit & Chat",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

-- Input for Target Player's Display Name (persistent)
MainTab:AddTextbox({
    Name = "Target Player (Display Name)",
    Default = "",
    TextDisappear = false, -- Persistent input
    Callback = function(Value)
        TargetDisplayName = Value
    end
})

-- Button to Remove Face
MainTab:AddButton({
    Name = "Remove Face",
    Callback = function()
        local LocalPlayer = game.Players.LocalPlayer
        local PlayerCharacter = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        RemoveFace(PlayerCharacter)
    end
})

-- Button to Start Sitting on Player's Head
MainTab:AddButton({
    Name = "Start Sitting Hiddenly",
    Callback = function()
        local TargetPlayer = GetPlayerByDisplayName(TargetDisplayName)
        local LocalPlayer = game.Players.LocalPlayer

        if TargetPlayer and TargetPlayer.Character and TargetPlayer.Character:FindFirstChild("Head") then
            local PlayerCharacter = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            ShrinkCharacter(PlayerCharacter)

            -- Teleport to Target Player's Head
            local TargetHead = TargetPlayer.Character.Head
            PlayerCharacter:SetPrimaryPartCFrame(TargetHead.CFrame + Vector3.new(0, 0.1, 0)) -- Slightly above the head

            -- Weld to Target Player's Head
            local Weld = Instance.new("WeldConstraint")
            Weld.Part0 = PlayerCharacter.PrimaryPart
            Weld.Part1 = TargetHead
            Weld.Parent = PlayerCharacter.PrimaryPart

            OrionLib:MakeNotification({
                Name = "Success",
                Content = "You are now hidden in " .. TargetDisplayName .. "'s head!",
                Image = "rbxassetid://4483345998",
                Time = 3
            })
        else
            OrionLib:MakeNotification({
                Name = "Error",
                Content = "Target player not found or head missing!",
                Image = "rbxassetid://4483345998",
                Time = 3
            })
        end
    end
})

-- Toggle to Monitor Chat
MainTab:AddToggle({
    Name = "Monitor Chat",
    Default = false,
    Callback = function(Value)
        if Value then
            for _, player in pairs(game:GetService("Players"):GetPlayers()) do
                player.Chatted:Connect(function(message)
                    OrionLib:MakeNotification({
                        Name = player.DisplayName .. " says:",
                        Content = message,
                        Image = "rbxassetid://4483345998",
                        Time = 5
                    })
                end)
            end

            game:GetService("Players").PlayerAdded:Connect(function(player)
                player.Chatted:Connect(function(message)
                    OrionLib:MakeNotification({
                        Name = player.DisplayName .. " says:",
                        Content = message,
                        Image = "rbxassetid://4483345998",
                        Time = 5
                    })
                end)
            end)
        else
            OrionLib:MakeNotification({
                Name = "Chat Monitoring Disabled",
                Content = "Chat monitoring has been turned off.",
                Image = "rbxassetid://4483345998",
                Time = 3
            })
        end
    end
})

-- Initialize OrionLib
OrionLib:Init()
