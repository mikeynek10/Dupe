local SG = Instance.new("ScreenGui", game.CoreGui) SG.Name = "FastResetGui"
local Kavo = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Kavo.CreateLib("Script Hub Của Bạn", "Midnight") -- Bạn có thể đổi màu: "DarkTheme", "Ocean", "Grape"
local Tab = Window:NewTab("Main")
local Sec = Tab:NewSection("Tính Năng Tự Động")

-- Tính năng ẩn (Ấn E ăn ngay) chạy ngầm tự động
pcall(function()
    local function inst(obj) if obj:IsA("ProximityPrompt") then obj.HoldDuration = 0 end end
    for _, v in pairs(game:GetDescendants()) do pcall(inst, v) end
    game.DescendantAdded:Connect(function(v) pcall(inst, v) end)
end)

-- Các nút bật/tắt tính năng
Sec:NewToggle("Auto Cash (Chuồng 1 - 40)", "Tự động thu thập tiền chuồng 1 đến 40", function(Value)
    _G.AutoCash = Value
    while _G.AutoCash do
        task.wait(0.1) 
        pcall(function()
            local stands = game.Players.LocalPlayer:FindFirstChild("AnimalStands")
            if stands then
                for i = 1, 40 do
                    if not _G.AutoCash then break end
                    local s = stands:FindFirstChild(tostring(i))
                    if s then game:GetService("ReplicatedStorage"):WaitForChild("Events"):WaitForChild("CollectCash"):FireServer(s) end
                end
            end
        end)
    end
end)

Sec:NewToggle("Tự Động Trùng Sinh (Auto Rebirth)", "Tự động trùng sinh khi đủ điều kiện", function(Value)
    _G.AutoRebirth = Value
    while _G.AutoRebirth do
        task.wait(1) 
        pcall(function() game:GetService("ReplicatedStorage"):WaitForChild("Events"):WaitForChild("Rebirth"):FireServer() end)
    end
end)

Sec:NewToggle("Tự Động Mua Tốc Độ (Auto Buy Speed)", "Tự động nâng cấp tốc độ chạy", function(Value)
    _G.AutoBuySpeed = Value
    while _G.AutoBuySpeed do
        task.wait(0.5) 
        pcall(function() game:GetService("ReplicatedStorage"):WaitForChild("Events"):WaitForChild("BuySpeed"):FireServer(5) end)
    end
end)

-- Nút Reset nhanh (R) dạng nổi di chuyển được bằng cách kéo thả
local RB = Instance.new("TextButton", SG) local UC = Instance.new("UICorner", RB)
RB.Name = "ResetButton" RB.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
RB.Position = UDim2.new(0.05, 0, 0.4, 0) RB.Size = UDim2.new(0, 50, 0, 50) 
RB.Font = Enum.Font.GothamBold RB.Text = "R" RB.TextColor3 = Color3.fromRGB(255, 255, 255)
RB.TextSize = 24 RB.ZIndex = 999 UC.CornerRadius = UDim.new(1, 0)
local UIS = game:GetService("UserInputService") local dragging, dragStart, startPos
RB.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging, dragStart, startPos = true, input.Position, RB.Position
    end
end)
UIS.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        RB.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
end)
RB.MouseButton1Click:Connect(function()
    local p = game.Players.LocalPlayer
    if p.Character and p.Character:FindFirstChild("Humanoid") then p.Character.Humanoid.Health = 0 end
end)
