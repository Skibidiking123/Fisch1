local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "AutoPetGui"
screenGui.ResetOnSpawn = false

local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 220, 0, 180)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, -30, 0, 30)
title.Position = UDim2.new(0, 10, 0, 5)
title.Text = "Auto Pet"
title.TextColor3 = Color3.fromRGB(240, 240, 255)
title.BackgroundTransparency = 1
title.TextSize = 18
title.Font = Enum.Font.Gotham

local closeButton = Instance.new("TextButton", frame)
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -30, 0, 5)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 80, 80)
closeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
closeButton.BorderSizePixel = 0
closeButton.Font = Enum.Font.Gotham
closeButton.TextSize = 16

local timingInput = Instance.new("TextBox", frame)
timingInput.Size = UDim2.new(1, -20, 0, 30)
timingInput.Position = UDim2.new(0, 10, 0, 40)
timingInput.PlaceholderText = "Enter timing (e.g. 1.5)"
timingInput.Text = ""
timingInput.TextColor3 = Color3.fromRGB(255, 255, 255)
timingInput.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
timingInput.BorderSizePixel = 0
timingInput.Font = Enum.Font.Gotham
timingInput.TextSize = 14

local startButton = Instance.new("TextButton", frame)
startButton.Size = UDim2.new(1, -20, 0, 35)
startButton.Position = UDim2.new(0, 10, 0, 80)
startButton.Text = "Start"
startButton.TextColor3 = Color3.fromRGB(255, 255, 255)
startButton.BackgroundColor3 = Color3.fromRGB(50, 140, 90)
startButton.BorderSizePixel = 0
startButton.Font = Enum.Font.Gotham
startButton.TextSize = 16

local stopButton = Instance.new("TextButton", frame)
stopButton.Size = UDim2.new(1, -20, 0, 35)
stopButton.Position = UDim2.new(0, 10, 0, 125)
stopButton.Text = "Stop"
stopButton.TextColor3 = Color3.fromRGB(255, 255, 255)
stopButton.BackgroundColor3 = Color3.fromRGB(150, 60, 60)
stopButton.BorderSizePixel = 0
stopButton.Font = Enum.Font.Gotham
stopButton.TextSize = 16

local running = false
local thread = nil

startButton.MouseButton1Click:Connect(function()
    if running then return end
    running = true
    local delayTime = tonumber(timingInput.Text) or 1.5

    thread = task.spawn(function()
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
            task.wait(4)
        end
    end)
end)

stopButton.MouseButton1Click:Connect(function()
    running = false
end)

closeButton.MouseButton1Click:Connect(function()
    running = false
    screenGui:Destroy()
end)
