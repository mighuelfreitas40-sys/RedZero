-- 🔴 REDZERO HUB Compact 🔴
-- Estilo Flamengo ⚫🔴

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Variáveis
local speedValue = 16
local speedActive = false
local infiniteJump = false
local watching = {}
local TpTool = nil

-- Função para criar botões
local function criarBotao(nome, parent, callback)
    local btn = Instance.new("TextButton")
    btn.Parent = parent
    btn.Size = UDim2.new(1, -10, 0, 30)
    btn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = nome
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    btn.AutoButtonColor = true
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- GUI
local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)

-- Botão flutuante
local toggleBtn = Instance.new("TextButton", ScreenGui)
toggleBtn.Size = UDim2.new(0, 100, 0, 35)
toggleBtn.Position = UDim2.new(0, 10, 0, 10)
toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.Text = "🔥 REDZERO 🔥"
toggleBtn.Active = true
toggleBtn.Draggable = true

-- Frame principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 320)
MainFrame.Position = UDim2.new(0, 10, 0, 60)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Active = true
MainFrame.Draggable = true

toggleBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Área dos botões
local buttonFrame = Instance.new("Frame", MainFrame)
buttonFrame.Size = UDim2.new(1, -10, 0, 150)
buttonFrame.Position = UDim2.new(0, 5, 0, 5)
buttonFrame.BackgroundTransparency = 1

-- Caixa para velocidade
local speedBox = Instance.new("TextBox", buttonFrame)
speedBox.Size = UDim2.new(1, 0, 0, 30)
speedBox.PlaceholderText = "Digite a velocidade"
speedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
speedBox.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
speedBox.Text = ""
speedBox.Font = Enum.Font.Gotham
speedBox.TextSize = 14
speedBox.FocusLost:Connect(function()
    local val = tonumber(speedBox.Text)
    if val then
        speedValue = math.clamp(val, 16, 150)
        if speedActive then
            humanoid.WalkSpeed = speedValue
        end
    end
end)

-- Botão de velocidade com troca de texto
local speedBtn = criarBotao("⚡ Ativar Velocidade", buttonFrame, function()
    speedActive = not speedActive
    if speedActive then
        humanoid.WalkSpeed = speedValue
        speedBtn.Text = "❌ Desligar Velocidade"
    else
        humanoid.WalkSpeed = 16
        speedBtn.Text = "⚡ Ativar Velocidade"
    end
end)
speedBtn.Position = UDim2.new(0, 0, 0, 35)

-- Botão de Infinite Jump com troca de texto
local jumpBtn = criarBotao("🦘 Ativar Pulo Infinito", buttonFrame, function()
    infiniteJump = not infiniteJump
    if infiniteJump then
        jumpBtn.Text = "❌ Desligar Pulo Infinito"
    else
        jumpBtn.Text = "🦘 Ativar Pulo Infinito"
    end
end)
jumpBtn.Position = UDim2.new(0, 0, 0, 70)

-- Script do Infinite Jump
UserInputService.JumpRequest:Connect(function()
    if infiniteJump then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- Botão para criar TpTool
local tpBtn = criarBotao("🌀 Criar TpTool", buttonFrame, function()
    if not TpTool then
        TpTool = Instance.new("Tool")
        TpTool.Name = "TpTool"
        TpTool.RequiresHandle = false
        TpTool.CanBeDropped = false

        TpTool.Activated:Connect(function()
            local mouse = player:GetMouse()
            if mouse.Target then
                character:MoveTo(mouse.Hit.Position)
            end
        end)

        TpTool.Parent = player.Backpack
        tpBtn.Text = "✔ TpTool Criada"
    end
end)
tpBtn.Position = UDim2.new(0, 0, 0, 105)

-- Lista de players para assistir
local playerListFrame = Instance.new("ScrollingFrame", MainFrame)
playerListFrame.Size = UDim2.new(1, -10, 0, 150)
playerListFrame.Position = UDim2.new(0, 5, 0, 160)
playerListFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
playerListFrame.ScrollBarThickness = 6
playerListFrame.CanvasSize = UDim2.new(0, 0, 0, 0)

local function atualizarListaPlayers()
    for _, child in pairs(playerListFrame:GetChildren()) do
        if child:IsA("Frame") then child:Destroy() end
    end
    local yPos = 5
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= player then
            local frame = Instance.new("Frame", playerListFrame)
            frame.Size = UDim2.new(1, -10, 0, 35)
            frame.Position = UDim2.new(0, 0, 0, yPos)
            frame.BackgroundTransparency = 1

            local nome = Instance.new("TextLabel", frame)
            nome.Size = UDim2.new(0.5, 0, 1, 0)
            nome.Text = "👤 " .. plr.Name
            nome.TextColor3 = Color3.fromRGB(255, 255, 255)
            nome.BackgroundTransparency = 1
            nome.TextXAlignment = Enum.TextXAlignment.Left

            local assistirBtn = Instance.new("TextButton", frame)
            assistirBtn.Size = UDim2.new(0.25, 0, 1, 0)
            assistirBtn.Position = UDim2.new(0.55, 0, 0, 0)
            assistirBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
            assistirBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
            assistirBtn.Text = "👀 Ver"
            assistirBtn.Font = Enum.Font.GothamBold

            local pararBtn = Instance.new("TextButton", frame)
            pararBtn.Size = UDim2.new(0.25, 0, 1, 0)
            pararBtn.Position = UDim2.new(0.8, 0, 0, 0)
            pararBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            pararBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
            pararBtn.Text = "❌ Parar"
            pararBtn.Visible = false
            pararBtn.Font = Enum.Font.GothamBold

            assistirBtn.MouseButton1Click:Connect(function()
                if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    workspace.CurrentCamera.CameraSubject = plr.Character.Humanoid
                    watching[plr] = true
                    pararBtn.Visible = true
                    assistirBtn.Visible = false
                end
            end)

            pararBtn.MouseButton1Click:Connect(function()
                workspace.CurrentCamera.CameraSubject = humanoid
                watching[plr] = nil
                pararBtn.Visible = false
                assistirBtn.Visible = true
            end)

            yPos = yPos + 40
        end
    end
    playerListFrame.CanvasSize = UDim2.new(0, 0, 0, yPos)
end

atualizarListaPlayers()
task.spawn(function()
    while task.wait(60) do
        atualizarListaPlayers()
    end
end)
