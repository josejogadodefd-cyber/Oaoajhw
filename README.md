-- [[ INTERFACE ADAPTADA PARA TOUCH/MOBILE ]]
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UIListLayout = Instance.new("UIListLayout")
local Title = Instance.new("TextLabel")
local MinBtn = Instance.new("TextButton") -- Botão de fechar/abrir

-- Configuração da GUI
ScreenGui.Parent = game:GetService("CoreGui")
ScreenGui.ResetOnSpawn = false

-- Janela Principal (Tamanho ideal para celular)
MainFrame.Name = "MobileMenu"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.Position = UDim2.new(0.1, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 180, 0, 250)
MainFrame.Active = true
MainFrame.Draggable = true -- Você pode arrastar com o dedo

-- Estilo do Título
Title.Parent = MainFrame
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "MOBILE HUB"
Title.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 20

-- Botão de Minimizar (Canto da tela)
MinBtn.Parent = ScreenGui
MinBtn.Size = UDim2.new(0, 50, 0, 50)
MinBtn.Position = UDim2.new(0, 10, 0.1, 0)
MinBtn.Text = "MENU"
MinBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinBtn.Visible = false -- Começa oculto

-- Função para abrir/fechar tocando no botão
local aberto = true
local function toggleMenu()
    aberto = not aberto
    MainFrame.Visible = aberto
    MinBtn.Visible = not aberto
end

-- Botão para fechar o menu (fica dentro do menu)
local CloseBtn = Instance.new("TextButton", MainFrame)
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.Text = "X"
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
CloseBtn.MouseButton1Click:Connect(toggleMenu)
MinBtn.MouseButton1Click:Connect(toggleMenu)

-- Layout dos Botões de Farm
local BtnContainer = Instance.new("ScrollingFrame", MainFrame)
BtnContainer.Size = UDim2.new(1, -10, 1, -50)
BtnContainer.Position = UDim2.new(0, 5, 0, 45)
BtnContainer.BackgroundTransparency = 1
BtnContainer.CanvasSize = UDim2.new(0, 0, 2, 0)

UIListLayout.Parent = BtnContainer
UIListLayout.Padding = UDim.new(0, 10) -- Espaço entre botões para não errar o toque

-- [[ FUNÇÃO PARA CRIAR BOTÕES DE MOBILE ]]
local function createMobileBtn(name, var)
    _G[var] = false
    local btn = Instance.new("TextButton", BtnContainer)
    btn.Size = UDim2.new(1, 0, 0, 45) -- Botão alto para facilitar o clique
    btn.Text = name .. ": OFF"
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 16

    btn.MouseButton1Click:Connect(function()
        _G[var] = not _G[var]
        btn.Text = _G[var] and name .. ": ON" or name .. ": OFF"
        btn.BackgroundColor3 = _G[var] and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(60, 60, 60)
    end)
end

-- Adicionando as funções de Farm
createMobileBtn("Auto Farm", "AutoFarm")
createMobileBtn("Bater Rápido", "FastAttack")
createMobileBtn("Puxar Bichos", "Magnet")
createMobileBtn("Pegar Frutas", "AutoFruit")
createMobileBtn("Pegar Baús", "AutoChest")

-- [[ LÓGICA DE ATAQUE MOBILE ]]
spawn(function()
    while task.wait() do
        if _G.AutoFarm then
            pcall(function()
                local char = game.Players.LocalPlayer.Character
                for _, v in pairs(game.Workspace.Enemies:GetChildren()) do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                        while _G.AutoFarm and v.Humanoid.Health > 0 do
                            task.wait()
                            -- Posicionamento em cima do NPC (evita travar no chão)
                            char.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame * CFrame.new(0, 8, 0)
                            
                            -- Magnet
                            if _G.Magnet then
                                for _, m in pairs(game.Workspace.Enemies:GetChildren()) do
                                    if m.Name == v.Name then
                                        m.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame
                                        m.HumanoidRootPart.CanCollide = false
                                    end
                                end
                            end
                            
                            -- Auto-Equip e Ataque Virtual
                            local tool = char:FindFirstChildOfClass("Tool") or game.Players.LocalPlayer.Backpack:FindFirstChildOfClass("Tool")
                            char.Humanoid:EquipTool(tool)
                            tool:Activate()
                            
                            -- Fast Attack para Ronix
                            if _G.FastAttack then
                                local cf = require(game:GetService("Players").LocalPlayer.PlayerScripts.CombatFramework)
                                cf.activeController.attackInterval = 0
                                cf.activeController.hitboxMagnitude = 60
                            end
                        end
                    end
                end
            end)
        end
    end
end)
