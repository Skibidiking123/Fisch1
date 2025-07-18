local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local StarterGui = game:GetService("StarterGui")

local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "AutoPetGui"

local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 200, 0, 150)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0

local timingInput = Instance.new("TextBox", frame)
timingInput.Size = UDim2.new(1, -20, 0, 30)
timingInput.Position = UDim2.new(0, 10, 0, 10)
timingInput.PlaceholderText = "Enter timing (e.g. 1.5)"
timingInput.Text = ""

local startButton = Instance.new("TextButton", frame)
startButton.Size = UDim2.new(1, -20, 0, 30)
startButton.Position = UDim2.new(0, 10, 0, 50)
startButton.Text = "Start"

local stopButton = Instance.new("TextButton", frame)
stopButton.Size = UDim2.new(1, -20, 0, 30)
stopButton.Position = UDim2.new(0, 10, 0, 90)
stopButton.Text = "Stop"

local running = false

startButton.MouseButton1Click:Connect(function()
    if running then return end
    running = true

    local delayTime = tonumber(timingInput.Text) or 1.5

    task.spawn(function()
        while running do
            local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            local Humanoid = Character:FindFirstChildOfClass("Humanoid")
            fireproximityprompt(workspace.NPCS.Steven.HumanoidRootPart.ProximityPrompt)

            local notfav = nil
            if Humanoid then
                local Backpack = LocalPlayer:WaitForChild("Backpack")
                for _, tool in ipairs(Backpack:GetChildren()) do
                    if tool:IsA("Tool") and tool:GetAttribute("ItemType") == "Pet" then
                        local favourited = tool:GetAttribute("d") 
                        if not favourited then 
                            Humanoid:EquipTool(tool)
                            notfav = tool
                            break
                        end
                    end
                end
            end

            task.wait(2)
            local gui = LocalPlayer.PlayerGui:FindFirstChild("Billboard_UI")
            if gui then
                local skibidi = gui:GetChildren()[4]
                if skibidi and skibidi:FindFirstChild("Frame") and skibidi.Frame:FindFirstChild("ImageButton") then
                    firesignal(skibidi.Frame.ImageButton.MouseButton1Click)
                end
            end

            task.wait(delayTime)
            if Humanoid and notfav then
                local equippedTool = Character:FindFirstChildOfClass("Tool")
                if equippedTool == notfav then
                    Humanoid:UnequipTools()
                    task.wait(0.3)
                    Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
            end
        end
    end)
end)

stopButton.MouseButton1Click:Connect(function()
    running = false
end)
