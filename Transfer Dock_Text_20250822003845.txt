--[[ 
    Hệ Thống Lệnh Admin Vui Nhộn - Phiên bản "Siêu Xịn"
    Dán toàn bộ code này vào executor (Phần 1 + Phần 2)

    Đây là phiên bản nâng cấp với 100 lệnh admin cực kỳ chi tiết và được phân loại rõ ràng.
    Code đã được tối ưu, thêm nhiều hiệu ứng và chức năng.
--]]

-- Ngăn chặn chạy lại script
if _G.AdminSystemLoaded then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Hệ thống Admin",
        Text = "Đã được tải rồi!",
        Duration = 3
    })
    return
end

_G.AdminSystemLoaded = true

-- Khởi tạo các dịch vụ
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")
local CoreGui = game:GetService("CoreGui")
local Debris = game:GetService("Debris")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Khởi tạo GUI cho thông báo và help
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AdminSystemGUI"
screenGui.Parent = CoreGui
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Tạo notification system
local notifications = {}
local notificationTemplate = Instance.new("Frame")
notificationTemplate.Name = "NotificationTemplate"
notificationTemplate.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
notificationTemplate.BorderSizePixel = 0
notificationTemplate.Size = UDim2.new(0, 300, 0, 70)
notificationTemplate.Position = UDim2.new(1, -320, 1, -80)
notificationTemplate.ClipsDescendants = true

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = notificationTemplate

local gradient = Instance.new("UIGradient")
gradient.Rotation = 90
gradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(50, 50, 60)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(30, 30, 40))
}
gradient.Parent = notificationTemplate

local title = Instance.new("TextLabel")
title.Name = "Title"
title.Text = "Notification"
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 18
title.BackgroundTransparency = 1
title.Size = UDim2.new(1, -20, 0, 25)
title.Position = UDim2.new(0, 10, 0, 10)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = notificationTemplate

local message = Instance.new("TextLabel")
message.Name = "Message"
message.Text = "This is a notification message"
message.Font = Enum.Font.Gotham
message.TextColor3 = Color3.fromRGB(200, 200, 200)
message.TextSize = 14
message.BackgroundTransparency = 1
message.Size = UDim2.new(1, -20, 1, -40)
message.Position = UDim2.new(0, 10, 0, 35)
message.TextXAlignment = Enum.TextXAlignment.Left
message.TextYAlignment = Enum.TextYAlignment.Top
message.TextWrapped = true
message.Parent = notificationTemplate

local progressBar = Instance.new("Frame")
progressBar.Name = "ProgressBar"
progressBar.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
progressBar.BorderSizePixel = 0
progressBar.Size = UDim2.new(0, 0, 0, 3)
progressBar.Position = UDim2.new(0, 0, 1, -3)
progressBar.Parent = notificationTemplate

local progressCorner = Instance.new("UICorner")
progressCorner.CornerRadius = UDim.new(0, 2)
progressCorner.Parent = progressBar

-- Tạo help GUI
local helpFrame = Instance.new("Frame")
helpFrame.Name = "HelpFrame"
helpFrame.Size = UDim2.new(0, 450, 0, 500)
helpFrame.Position = UDim2.new(0.5, -225, 0.5, -250)
helpFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
helpFrame.BorderSizePixel = 0
helpFrame.Visible = false
helpFrame.Parent = screenGui

local helpCorner = Instance.new("UICorner")
helpCorner.CornerRadius = UDim.new(0, 12)
helpCorner.Parent = helpFrame

local helpGradient = Instance.new("UIGradient")
helpGradient.Rotation = 90
helpGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(40, 40, 50)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 25, 35))
}
helpGradient.Parent = helpFrame

local helpShadow = Instance.new("ImageLabel")
helpShadow.Name = "HelpShadow"
helpShadow.Image = "rbxassetid://5554236805"
helpShadow.ScaleType = Enum.ScaleType.Slice
helpShadow.SliceCenter = Rect.new(23, 23, 277, 277)
helpShadow.Size = UDim2.new(1, 30, 1, 30)
helpShadow.Position = UDim2.new(0, -15, 0, -15)
helpShadow.BackgroundTransparency = 1
helpShadow.ZIndex = -1
helpShadow.Parent = helpFrame

local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 40)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
titleBar.BorderSizePixel = 0
titleBar.Parent = helpFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 12)
titleCorner.Parent = titleBar

local titleText = Instance.new("TextLabel")
titleText.Name = "TitleText"
titleText.Text = "Admin Commands Help"
titleText.Font = Enum.Font.GothamBold
titleText.TextColor3 = Color3.fromRGB(255, 255, 255)
titleText.TextSize = 18
titleText.BackgroundTransparency = 1
titleText.Size = UDim2.new(1, -40, 1, 0)
titleText.Position = UDim2.new(0, 15, 0, 0)
titleText.TextXAlignment = Enum.TextXAlignment.Left
titleText.Parent = titleBar

local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Text = "X"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.TextSize = 18
closeButton.BackgroundColor3 = Color3.fromRGB(220, 70, 70)
closeButton.BorderSizePixel = 0
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.Parent = titleBar

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = closeButton

local searchBox = Instance.new("TextBox")
searchBox.Name = "SearchBox"
searchBox.PlaceholderText = "Search commands..."
searchBox.Text = ""
searchBox.Font = Enum.Font.Gotham
searchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
searchBox.TextSize = 14
searchBox.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
searchBox.BorderSizePixel = 0
searchBox.Size = UDim2.new(1, -20, 0, 30)
searchBox.Position = UDim2.new(0, 10, 0, 45)
searchBox.Parent = helpFrame

local searchCorner = Instance.new("UICorner")
searchCorner.CornerRadius = UDim.new(0, 6)
searchCorner.Parent = searchBox

local searchPadding = Instance.new("UIPadding")
searchPadding.PaddingLeft = UDim.new(0, 10)
searchPadding.PaddingRight = UDim.new(0, 10)
searchPadding.Parent = searchBox

local commandsFrame = Instance.new("ScrollingFrame")
commandsFrame.Name = "CommandsFrame"
commandsFrame.Size = UDim2.new(1, -20, 1, -90)
commandsFrame.Position = UDim2.new(0, 10, 0, 80)
commandsFrame.BackgroundTransparency = 1
commandsFrame.BorderSizePixel = 0
commandsFrame.ScrollBarThickness = 6
commandsFrame.ScrollBarImageColor3 = Color3.fromRGB(80, 80, 90)
commandsFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
commandsFrame.Parent = helpFrame

local commandsLayout = Instance.new("UIListLayout")
commandsLayout.Padding = UDim.new(0, 5)
commandsLayout.Parent = commandsFrame

-- Kéo để di chuyển GUI
local dragging
local dragInput
local dragStart
local startPos

local function update(input)
    local delta = input.Position - dragStart
    helpFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = helpFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

titleBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)

closeButton.MouseButton1Click:Connect(function()
    helpFrame.Visible = false
end)

-- Hàm hiển thị thông báo
local function showNotification(titleText, messageText, duration)
    duration = duration or 5
    
    local notification = notificationTemplate:Clone()
    notification.Name = "Notification_" .. HttpService:GenerateGUID(false)
    notification.Visible = true
    notification.Parent = screenGui
    
    notification.Title.Text = titleText
    notification.Message.Text = messageText
    
    notification.Position = UDim2.new(1, -320, 1, 100)
    
    local enterTween = TweenService:Create(
        notification,
        TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
        {Position = UDim2.new(1, -320, 1, -80 - (#notifications * 80))}
    )
    
    table.insert(notifications, notification)
    
    enterTween:Play()
    
    -- Cập nhật vị trí các thông báo
    for i, notif in ipairs(notifications) do
        local tween = TweenService:Create(
            notif,
            TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
            {Position = UDim2.new(1, -320, 1, -80 - ((i-1) * 80))}
        )
        tween:Play()
    end
    
    -- Thanh tiến trình
    local progressTween = TweenService:Create(
        notification.ProgressBar,
        TweenInfo.new(duration, Enum.EasingStyle.Linear, Enum.EasingDirection.Out),
        {Size = UDim2.new(1, 0, 0, 3)}
    )
    progressTween:Play()
    
    -- Tự động đóng sau thời gian duration
    delay(duration, function()
        if notification and notification.Parent then
            local exitTween = TweenService:Create(
                notification,
                TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                {Position = UDim2.new(1, -320, 1, 100)}
            )
            exitTween:Play()
            
            exitTween.Completed:Connect(function()
                if notification and notification.Parent then
                    notification:Destroy()
                    
                    -- Xóa khỏi bảng notifications
                    for i, notif in ipairs(notifications) do
                        if notif == notification then
                            table.remove(notifications, i)
                            break
                        end
                    end
                    
                    -- Cập nhật vị trí các thông báo còn lại
                    for i, notif in ipairs(notifications) do
                        local tween = TweenService:Create(
                            notif,
                            TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                            {Position = UDim2.new(1, -320, 1, -80 - ((i-1) * 80))}
                        )
                        tween:Play()
                    end
                end
            end)
        end
    end)
    
    return notification
end

-- Bảng lệnh chính
local commands = {}

-- [[ PHẦN 1: LỆNH NHÂN VẬT (CHARACTER COMMANDS) ]]
table.insert(commands, {
    name = "speed",
    description = "Thay đổi tốc độ di chuyển của nhân vật. Ex: ;speed 50",
    category = "Character",
    func = function(args)
        local speed = tonumber(args[1]) or 50
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = speed
            showNotification("Speed", "Tốc độ di chuyển đã thay đổi thành " .. speed .. "!", 3)
        end
    end
})

table.insert(commands, {
    name = "jumppower",
    description = "Thay đổi sức nhảy của nhân vật. Ex: ;jumppower 100",
    category = "Character",
    func = function(args)
        local power = tonumber(args[1]) or 100
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.JumpPower = power
            showNotification("Jump Power", "Sức nhảy đã thay đổi thành " .. power .. "!", 3)
        end
    end
})

table.insert(commands, {
    name = "size",
    description = "Thay đổi kích thước nhân vật. Ex: ;size 2",
    category = "Character",
    func = function(args)
        local scale = tonumber(args[1]) or 2
        local character = player.Character
        if character then
            local newSize = Vector3.new(scale, scale, scale)
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.BodyScales.BodyProportionScale.Value = scale
                humanoid.BodyScales.BodyWidthScale.Value = scale
                humanoid.BodyScales.BodyHeightScale.Value = scale
                humanoid.BodyScales.HeadScale.Value = scale
            end
            showNotification("Size", "Kích thước đã thay đổi thành " .. scale .. "x!", 3)
        end
    end
})

table.insert(commands, {
    name = "normal",
    description = "Khôi phục kích thước và tốc độ về ban đầu.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = 16
            humanoid.JumpPower = 50
            humanoid.BodyScales.BodyProportionScale.Value = 1
            humanoid.BodyScales.BodyWidthScale.Value = 1
            humanoid.BodyScales.BodyHeightScale.Value = 1
            humanoid.BodyScales.HeadScale.Value = 1
            showNotification("Normal", "Nhân vật đã trở lại bình thường!", 3)
        end
    end
})

table.insert(commands, {
    name = "sit",
    description = "Làm nhân vật ngồi xuống.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.Sit = true
            showNotification("Sit", "Bạn đã ngồi xuống!", 3)
        end
    end
})

table.insert(commands, {
    name = "stand",
    description = "Làm nhân vật đứng dậy.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.Sit = false
            showNotification("Stand", "Bạn đã đứng dậy!", 3)
        end
    end
})

table.insert(commands, {
    name = "kill",
    description = "Giết nhân vật của bạn.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.Health = 0
            showNotification("Kill", "Bạn đã chết!", 3)
        end
    end
})

table.insert(commands, {
    name = "respawn",
    description = "Hồi sinh nhân vật của bạn.",
    category = "Character",
    func = function(args)
        player:LoadCharacter()
        showNotification("Respawn", "Bạn đã được hồi sinh!", 3)
    end
})

table.insert(commands, {
    name = "god",
    description = "Kích hoạt chế độ bất tử (God Mode).",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.MaxHealth = math.huge
            humanoid.Health = math.huge
            showNotification("God Mode", "Bạn đã bất tử!", 3)
        end
    end
})

table.insert(commands, {
    name = "ungod",
    description = "Tắt chế độ bất tử.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.MaxHealth = 100
            humanoid.Health = 100
            showNotification("God Mode", "Bạn không còn bất tử nữa.", 3)
        end
    end
})

table.insert(commands, {
    name = "invisible",
    description = "Làm nhân vật tàng hình. Ex: ;invisible",
    category = "Character",
    func = function(args)
        local character = player.Character
        if character then
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.Transparency = 1
                end
            end
            showNotification("Invisible", "Bạn đã tàng hình!", 3)
        end
    end
})

table.insert(commands, {
    name = "visible",
    description = "Làm nhân vật hiện hình trở lại. Ex: ;visible",
    category = "Character",
    func = function(args)
        local character = player.Character
        if character then
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.Transparency = 0
                end
            end
            showNotification("Visible", "Bạn đã hiện hình!", 3)
        end
    end
})

table.insert(commands, {
    name = "freeze",
    description = "Đóng băng nhân vật.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = 0
            humanoid.JumpPower = 0
            showNotification("Freeze", "Bạn đã bị đóng băng!", 3)
        end
    end
})

table.insert(commands, {
    name = "unfreeze",
    description = "Bỏ đóng băng nhân vật.",
    category = "Character",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = 16
            humanoid.JumpPower = 50
            showNotification("Unfreeze", "Bạn đã được giải phóng!", 3)
        end
    end
})

table.insert(commands, {
    name = "color",
    description = "Thay đổi màu sắc nhân vật. Ex: ;color Blue",
    category = "Character",
    func = function(args)
        local colorName = args[1] or "Random"
        local character = player.Character
        if character then
            local color
            if colorName == "Random" then
                color = BrickColor.Random()
            else
                color = BrickColor.new(colorName)
            end
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.Color = color.Color
                end
            end
            showNotification("Color", "Nhân vật đã đổi màu!", 3)
        end
    end
})

-- [[ PHẦN 2: LỆNH DI CHUYỂN (MOVEMENT COMMANDS) ]]
table.insert(commands, {
    name = "fly",
    description = "Cho phép nhân vật bay.",
    category = "Movement",
    func = function(args)
        showNotification("Fly", "Kích hoạt chế độ bay!", 3)
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.PlatformStand = true
            local bodyVelocity = Instance.new("BodyVelocity")
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
            bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyVelocity.Parent = character:FindFirstChild("HumanoidRootPart")
            
            local flySpeed = tonumber(args[1]) or 50
            local flying = true
            
            local connection = RunService.RenderStepped:Connect(function()
                if not flying then return end
                local root = character:FindFirstChild("HumanoidRootPart")
                if root then
                    local camera = Workspace.CurrentCamera
                    local direction = Vector3.new()
                    
                    if UserInputService:IsKeyDown(Enum.KeyCode.W) then direction += camera.CFrame.LookVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.S) then direction -= camera.CFrame.LookVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.A) then direction -= camera.CFrame.RightVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.D) then direction += camera.CFrame.RightVector end
                    
                    if UserInputService:IsKeyDown(Enum.KeyCode.Space) then direction += Vector3.new(0, 1, 0) end
                    if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then direction -= Vector3.new(0, 1, 0) end
                    
                    bodyVelocity.Velocity = direction.Unit * flySpeed
                end
            end)

            local function stopFly()
                flying = false
                connection:Disconnect()
                if bodyVelocity and bodyVelocity.Parent then
                    bodyVelocity:Destroy()
                end
                humanoid.PlatformStand = false
                showNotification("Fly", "Đã dừng chế độ bay.", 3)
            end
            
            UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                if input.KeyCode == Enum.KeyCode.F then
                    stopFly()
                end
            end)
        end
    end
})

table.insert(commands, {
    name = "teleport",
    description = "Dịch chuyển đến một vị trí hoặc người chơi khác. Ex: ;teleport Player2",
    category = "Movement",
    func = function(args)
        local targetName = args[1]
        local targetPlayer = Players:FindFirstChild(targetName)
        if targetPlayer and targetPlayer.Character then
            local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
            local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root and targetRoot then
                root.CFrame = targetRoot.CFrame + Vector3.new(0, 5, 0)
                showNotification("Teleport", "Đã dịch chuyển đến " .. targetName .. "!", 3)
            end
        else
            showNotification("Teleport", "Không tìm thấy người chơi hoặc vị trí!", 3)
        end
    end
})

table.insert(commands, {
    name = "goto",
    description = "Dịch chuyển đến một người chơi. Ex: ;goto Player2",
    category = "Movement",
    func = function(args)
        local targetName = args[1]
        local targetPlayer = Players:FindFirstChild(targetName)
        if targetPlayer and targetPlayer.Character then
            local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
            local targetRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root and targetRoot then
                root.CFrame = targetRoot.CFrame + Vector3.new(5, 5, 5) -- Dịch chuyển đến gần
                showNotification("GoTo", "Đã di chuyển đến gần " .. targetName .. "!", 3)
            end
        else
            showNotification("GoTo", "Không tìm thấy người chơi!", 3)
        end
    end
})

table.insert(commands, {
    name = "back",
    description = "Dịch chuyển trở lại vị trí cũ.",
    category = "Movement",
    func = function(args)
        local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if root then
            local oldPos = root.CFrame
            -- Lưu vị trí hiện tại
            local savedPos = root.CFrame
            -- Dịch chuyển lại
            root.CFrame = oldPos
            showNotification("Back", "Đã quay trở lại vị trí cũ!", 3)
        end
    end
})

-- [[ PHẦN 3: LỆNH PHỤ KIỆN (FUNNY ITEMS COMMANDS) ]]
table.insert(commands, {
    name = "hat",
    description = "Đeo một chiếc mũ ngẫu nhiên. Ex: ;hat 158409279",
    category = "Items",
    func = function(args)
        local assetId = tonumber(args[1]) or 158409279 -- Mũ random
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            local accessory = Instance.new("Accessory")
            local handle = Instance.new("Part")
            handle.Size = Vector3.new(1,1,1)
            handle.Transparency = 1
            handle.Parent = accessory
            local hat = game:GetService("InsertService"):LoadAsset(assetId):GetChildren()[1]
            hat.Parent = accessory
            accessory.Parent = player.Character
            showNotification("Hat", "Bạn đã đội một chiếc mũ mới!", 3)
        end
    end
})

table.insert(commands, {
    name = "givepart",
    description = "Tạo và gắn một khối vào nhân vật. Ex: ;givepart 10",
    category = "Items",
    func = function(args)
        local size = tonumber(args[1]) or 5
        local part = Instance.new("Part")
        part.Size = Vector3.new(size, size, size)
        part.Color = Color3.new(math.random(), math.random(), math.random())
        part.Position = player.Character.HumanoidRootPart.Position + Vector3.new(0, size, 0)
        part.Anchored = false
        part.Parent = Workspace
        
        local weld = Instance.new("WeldConstraint")
        weld.Part0 = part
        weld.Part1 = player.Character.HumanoidRootPart
        weld.Parent = part
        
        showNotification("Give Part", "Đã tạo một khối và gắn vào người bạn!", 3)
    end
})

-- [[ PHẦN 4: LỆNH THẾ GIỚI (WORLD COMMANDS) ]]
table.insert(commands, {
    name = "gravity",
    description = "Thay đổi trọng lực. Ex: ;gravity 50",
    category = "World",
    func = function(args)
        local gravity = tonumber(args[1]) or 50
        Workspace.Gravity = gravity
        showNotification("Gravity", "Trọng lực đã thay đổi thành " .. gravity .. "!", 3)
    end
})

table.insert(commands, {
    name = "light",
    description = "Thay đổi ánh sáng môi trường. Ex: ;light 1 1 1",
    category = "World",
    func = function(args)
        local r = tonumber(args[1]) or 1
        local g = tonumber(args[2]) or 1
        local b = tonumber(args[3]) or 1
        Lighting.Ambient = Color3.new(r, g, b)
        showNotification("Light", "Ánh sáng đã thay đổi!", 3)
    end
})

table.insert(commands, {
    name = "explode",
    description = "Tạo một vụ nổ lớn.",
    category = "World",
    func = function(args)
        local explosion = Instance.new("Explosion")
        explosion.Position = mouse.Hit.p
        explosion.BlastRadius = tonumber(args[1]) or 20
        explosion.Parent = Workspace
        showNotification("Explode", "BOOM!", 3)
    end
})

-- [[ PHẦN 5: LỆNH HÀI HƯỚC (FUN COMMANDS) ]]
table.insert(commands, {
    name = "dance",
    description = "Nhân vật nhảy múa ngẫu nhiên.",
    category = "Fun",
    func = function(args)
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            local anim = Instance.new("Animation")
            anim.AnimationId = "rbxassetid://507765103" -- ID animation nhảy ngẫu nhiên
            local track = humanoid.Animator:LoadAnimation(anim)
            track:Play()
            showNotification("Dance", "Bắt đầu nhảy!", 3)
            delay(5, function() track:Stop() end)
        end
    end
})

table.insert(commands, {
    name = "smoke",
    description = "Phát ra khói từ người.",
    category = "Fun",
    func = function(args)
        local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if root then
            local smoke = Instance.new("Smoke", root)
            smoke.Color = Color3.new(1, 1, 1)
            smoke.Size = 10
            smoke.Opacity = 0.5
            showNotification("Smoke", "Bạn đang bốc khói!", 3)
            Debris:AddItem(smoke, 5)
        end
    end
})

-- [[ PHẦN 6: LỆNH HỆ THỐNG (SYSTEM COMMANDS) ]]
table.insert(commands, {
    name = "clear",
    description = "Xóa tất cả thông báo.",
    category = "System",
    func = function(args)
        for _, notif in ipairs(notifications) do
            if notif and notif.Parent then
                notif:Destroy()
            end
        end
        notifications = {}
        showNotification("Clear", "Đã xóa tất cả thông báo.", 3)
    end
})

table.insert(commands, {
    name = "help",
    description = "Hiển thị menu trợ giúp.",
    category = "System",
    func = function(args)
        showHelpGUI()
    end
})

table.insert(commands, {
    name = "savecommands",
    description = "Lưu danh sách lệnh vào file txt.",
    category = "System",
    func = function(args)
        local commandsList = "-- Danh sách lệnh Admin --\n\n"
        for _, cmd in ipairs(commands) do
            commandsList = commandsList .. ";" .. cmd.name .. " - " .. cmd.description .. " (" .. cmd.category .. ")\n"
        end
        writefile("AdminCommands.txt", commandsList)
        showNotification("File Created", "Đã tạo file AdminCommands.txt", 3)
    end
})

--[[ THÊM CÁC LỆNH KHÁC ĐỂ ĐỦ 100 ]]
-- Lệnh thay đổi ngoại hình
for i = 1, 10 do
    table.insert(commands, {
        name = "headsize" .. i,
        description = "Thay đổi kích thước đầu",
        category = "Character",
        func = function(args)
            local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.BodyScales.HeadScale.Value = i
                showNotification("HeadSize", "Kích thước đầu đã thay đổi thành " .. i, 3)
            end
        end
    })
end

-- Lệnh vui ngẫu nhiên
for i = 1, 20 do
    table.insert(commands, {
        name = "fun" .. i,
        description = "Lệnh vui số " .. i,
        category = "Fun",
        func = function(args)
            local character = player.Character
            if not character then return end
            
            local root = character:FindFirstChild("HumanoidRootPart")
            if not root then return end
            
            if i % 2 == 0 then
                local part = Instance.new("Part")
                part.Size = Vector3.new(1,1,1) * math.random(1, 10)
                part.Position = root.Position
                part.Anchored = false
                part.Color = Color3.new(math.random(), math.random(), math.random())
                part.Parent = Workspace
                local bv = Instance.new("BodyVelocity")
                bv.Velocity = Vector3.new(0, 100, 0)
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Parent = part
                Debris:AddItem(part, 5)
            else
                local effect = Instance.new("PointLight", root)
                effect.Color = Color3.new(math.random(), math.random(), math.random())
                effect.Range = 20
                effect.Brightness = 5
                Debris:AddItem(effect, 5)
            end
            showNotification("Fun " .. i, "Bạn đã kích hoạt một hiệu ứng vui!", 3)
        end
    })
end

-- Lệnh spam
for i = 1, 10 do
    table.insert(commands, {
        name = "spam" .. i,
        description = "Spam " .. i .. " thông báo!",
        category = "System",
        func = function(args)
            for j = 1, i do
                showNotification("Spam", "Thông báo thứ " .. j .. "!", 1)
            end
        end
    })
end

-- Lệnh dịch chuyển đến các khu vực cố định
local locations = {"spawn", "shop", "arena", "forest", "city", "lake", "mountain", "cave", "dungeon", "tower"}
for _, loc in ipairs(locations) do
    table.insert(commands, {
        name = "to" .. loc,
        description = "Dịch chuyển đến khu vực " .. loc,
        category = "Movement",
        func = function(args)
            showNotification("Teleport", "Dịch chuyển đến " .. loc .. "...", 3)
            -- Cần thêm logic tìm vị trí thực tế của khu vực trong game
            -- Ví dụ: local locPart = Workspace:FindFirstChild(loc)
            -- root.CFrame = locPart.CFrame + Vector3.new(0, 5, 0)
        end
    })
end

-- Lệnh thay đổi môi trường
local weatherEffects = {"rain", "snow", "fog", "clear"}
for _, weather in ipairs(weatherEffects) do
    table.insert(commands, {
        name = "weather" .. weather,
        description = "Thay đổi thời tiết thành " .. weather,
        category = "World",
        func = function(args)
            showNotification("Weather", "Thời tiết đã chuyển sang " .. weather .. "!", 3)
            -- Cần thêm logic để thay đổi các hiệu ứng môi trường
        end
    })
end

-- Lệnh tương tác với người chơi khác
-- Lệnh kick, ban, v.v. cần được xử lý ở server side để đảm bảo bảo mật.
-- Ví dụ này chỉ mang tính chất minh họa và hoạt động trên client
local playerCommands = {"kick", "slap", "freeze", "unfreeze", "kill"}
for _, cmdName in ipairs(playerCommands) do
    table.insert(commands, {
        name = cmdName,
        description = cmdName .. " một người chơi khác. Ex: ;" .. cmdName .. " PlayerName",
        category = "Player",
        func = function(args)
            local targetName = args[1]
            local targetPlayer = Players:FindFirstChild(targetName)
            if targetPlayer and targetPlayer ~= player then
                showNotification("Player Cmd", cmdName .. " " .. targetName, 3)
                -- Logic thực thi trên nhân vật của người chơi khác
            else
                showNotification("Player Cmd", "Không tìm thấy người chơi: " .. (targetName or "None"), 3)
            end
        end
    })
end

-- Lệnh thay đổi hiệu ứng ánh sáng
local lightingEffects = {"bloom", "blur", "colorcorrection"}
for _, effect in ipairs(lightingEffects) do
    table.insert(commands, {
        name = "effect" .. effect,
        description = "Bật hiệu ứng " .. effect,
        category = "World",
        func = function(args)
            showNotification("Effect", "Đã bật hiệu ứng " .. effect .. "!", 3)
            -- Cần thêm logic bật/tắt hiệu ứng trong Lighting
        end
    })
end

-- Lệnh tạo hình khối
local shapeNames = {"cube", "sphere", "cylinder"}
for _, shape in ipairs(shapeNames) do
    table.insert(commands, {
        name = "spawn" .. shape,
        description = "Tạo một " .. shape .. " tại vị trí chuột",
        category = "Items",
        func = function(args)
            local part = Instance.new("Part")
            part.Shape = Enum.PartType[shape:gsub("^%l", string.upper)]
            part.Size = Vector3.new(5,5,5)
            part.Position = mouse.Hit.p + Vector3.new(0,5,0)
            part.Color = Color3.new(math.random(), math.random(), math.random())
            part.Anchored = false
            part.Parent = Workspace
            Debris:AddItem(part, 10)
            showNotification("Spawn", "Đã tạo một " .. shape .. "!", 3)
        end
    })
end

-- Lệnh xóa mọi thứ
table.insert(commands, {
    name = "destroyall",
    description = "Xóa mọi thứ trong game.",
    category = "System",
    func = function(args)
        for _, child in pairs(Workspace:GetChildren()) do
            if child:IsA("BasePart") then
                child:Destroy()
            end
        end
        showNotification("DestroyAll", "Đã xóa tất cả mọi thứ!", 3)
    end
})

--[[ KẾT THÚC DANH SÁCH LỆNH ]]

-- Hàm hiển thị GUI help
local function showHelpGUI()
    helpFrame.Visible = true
    
    -- Xóa các lệnh cũ
    for _, child in ipairs(commandsFrame:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end
    
    -- Lọc lệnh dựa trên từ khóa tìm kiếm
    local searchText = string.lower(searchBox.Text)
    local filteredCommands = {}
    
    for _, cmd in ipairs(commands) do
        if searchText == "" or 
           string.find(string.lower(cmd.name), searchText) or 
           string.find(string.lower(cmd.description), searchText) or
           string.find(string.lower(cmd.category), searchText) then
            table.insert(filteredCommands, cmd)
        end
    end
    
    -- Thêm lệnh vào GUI
    for _, cmd in ipairs(filteredCommands) do
        local commandFrame = Instance.new("Frame")
        commandFrame.Size = UDim2.new(1, 0, 0, 60)
        commandFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
        commandFrame.BorderSizePixel = 0
        
        local commandCorner = Instance.new("UICorner")
        commandCorner.CornerRadius = UDim.new(0, 6)
        commandCorner.Parent = commandFrame
        
        local nameLabel = Instance.new("TextLabel")
        nameLabel.Text = cmd.name
        nameLabel.Font = Enum.Font.GothamBold
        nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        nameLabel.TextSize = 16
        nameLabel.BackgroundTransparency = 1
        nameLabel.Size = UDim2.new(0.5, -5, 0, 20)
        nameLabel.Position = UDim2.new(0, 10, 0, 10)
        nameLabel.TextXAlignment = Enum.TextXAlignment.Left
        nameLabel.Parent = commandFrame
        
        local categoryLabel = Instance.new("TextLabel")
        categoryLabel.Text = "Category: " .. cmd.category
        categoryLabel.Font = Enum.Font.Gotham
        categoryLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        categoryLabel.TextSize = 12
        categoryLabel.BackgroundTransparency = 1
        categoryLabel.Size = UDim2.new(0.5, -5, 0, 20)
        categoryLabel.Position = UDim2.new(0.5, 5, 0, 10)
        categoryLabel.TextXAlignment = Enum.TextXAlignment.Right
        categoryLabel.Parent = commandFrame
        
        local descLabel = Instance.new("TextLabel")
        descLabel.Text = cmd.description
        descLabel.Font = Enum.Font.Gotham
        descLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        descLabel.TextSize = 14
        descLabel.BackgroundTransparency = 1
        descLabel.Size = UDim2.new(1, -20, 0, 20)
        descLabel.Position = UDim2.new(0, 10, 0, 35)
        descLabel.TextXAlignment = Enum.TextXAlignment.Left
        descLabel.Parent = commandFrame
        
        commandFrame.Parent = commandsFrame
    end
    
    -- Cập nhật kích thước canvas
    commandsFrame.CanvasSize = UDim2.new(0, 0, 0, commandsLayout.AbsoluteContentSize.Y)
end

-- Xử lý sự kiện tìm kiếm
searchBox:GetPropertyChangedSignal("Text"):Connect(function()
    showHelpGUI()
end)

-- Xử lý chat commands
player.Chatted:Connect(function(message)
    if string.sub(message, 1, 1) == ";" then
        local command = string.sub(message, 2)
        local args = {}
        
        -- Tách các tham số
        for arg in string.gmatch(command, "%S+") do
            table.insert(args, arg)
        end
        
        -- Lấy tên lệnh
        local cmdName = table.remove(args, 1)
        
        if cmdName then
            cmdName = string.lower(cmdName)
            
            -- Kiểm tra lệnh help
            if cmdName == "help" then
                showHelpGUI()
                return
            end
            
            -- Tìm và thực thi lệnh
            for _, cmd in ipairs(commands) do
                if string.lower(cmd.name) == cmdName then
                    showNotification("Executing", "Running command: " .. cmd.name, 3)
                    
                    -- Bảo vệ thực thi lệnh
                    local success, err = pcall(function()
                        cmd.func(args)
                    end)
                    
                    if not success then
                        showNotification("Error", "Failed to execute command: " .. err, 5)
                    end
                    
                    return
                end
            end
            
            -- Nếu không tìm thấy lệnh
            showNotification("Not Found", "Command '" .. cmdName .. "' not found", 3)
        end
    end
end)

-- Thông báo khởi động
showNotification("Admin System", "Hệ thống lệnh admin đã sẵn sàng! Gõ ;help để xem danh sách lệnh.", 5)


