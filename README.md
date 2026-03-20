-- [[ BANANA HUB PRIVADO - EDIÇÃO LEVE VNG/RONIX ]]
local ScreenGui = Instance.new("ScreenGui", game:GetService("CoreGui"))
local Main = Instance.new("Frame", ScreenGui)
local Title = Instance.new("TextLabel", Main)
local FarmBtn = Instance.new("TextButton", Main)
local StatBtn = Instance.new("TextButton", Main)

-- Configuração da Janela (Estilo Amarelo Banana)
Main.Size = UDim2.new(0, 180, 0, 150)
Main.Position = UDim2.new(0.4, 0, 0.4, 0)
Main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Main.BorderSizePixel = 2
Main.BorderColor3 = Color3.fromRGB(255, 255, 0) -- Amarelo
Main.Active = true
Main.Draggable = true

Title.Size = UDim2.new(1, 0, 0, 35)
Title.Text = "BANANA PRIVADO"
Title.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
Title.TextColor3 = Color3.fromRGB(0, 0, 0)
Title.TextSize = 18
Title.Font = Enum.Font.SourceSansBold

-- Botão de Auto Farm
FarmBtn.Size = UDim2.new(0.9, 0, 0, 40)
FarmBtn.Position = UDim2.new(0.05, 0, 0.35, 0)
FarmBtn.Text = "AUTO FARM: OFF"
FarmBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
FarmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Botão de Auto Status (Soco/Melee)
StatBtn.Size = UDim2.new(0.9, 0, 0, 40)
StatBtn.Position = UDim2.new(0.05, 0, 0.68, 0)
StatBtn.Text = "AUTO STATUS: OFF"
StatBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
StatBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

-- VARIÁVEIS GLOBAIS
_G.AutoFarm = false
_G.AutoStats = false

-- LÓGICA DOS BOTÕES
FarmBtn.MouseButton1Click:Connect(function()
    _G.AutoFarm = not _G.AutoFarm
    FarmBtn.Text = _G.AutoFarm and "AUTO FARM: ON" or "AUTO FARM: OFF"
    FarmBtn.BackgroundColor3 = _G.AutoFarm and Color3.fromRGB(0, 180, 50) or Color3.fromRGB(45, 45, 45)
end)

StatBtn.MouseButton1Click:Connect(function()
    _G.AutoStats = not _G.AutoStats
    StatBtn.Text = _G.AutoStats and "STATS: ON" or "STATS: OFF"
end)

-- [[ FUNÇÃO DE COMBATE (MAGNET + HITBOX) ]]
spawn(function()
    while task.wait(0.1) do
        if _G.AutoFarm then
            pcall(function()
                local player = game.Players.LocalPlayer
                local char = player.Character
                
                -- Busca NPCs próximos no Workspace
                for _, v in pairs(game.Workspace.Enemies:GetChildren()) do
                    if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                        
                        -- MAGNET: Puxa o inimigo para sua frente
                        v.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame * CFrame.new(0, 0, -6)
                        v.HumanoidRootPart.CanCollide = false
                        v.HumanoidRootPart.Velocity = Vector3.new(0,0,0)
                        
                        -- HITBOX: Aumenta o tamanho do bicho para acertar fácil
                        v.HumanoidRootPart.Size = Vector3.new(40, 40, 40)
                        v.HumanoidRootPart.Transparency = 0.8 -- Fica semi-transparente
                        
                        -- EQUIPAR ARMA AUTOMATICAMENTE
                        local tool = player.Backpack:FindFirstChildOfClass("Tool") or char:FindFirstChildOfClass("Tool")
                        if tool then
                            char.Humanoid:EquipTool(tool)
                            tool:Activate() -- Soco/Espada
                            
                            -- Clique Virtual para reforçar o ataque no Ronix
                            game:GetService("VirtualUser"):CaptureController()
                            game:GetService("VirtualUser"):Button1Down(Vector2.new(100, 100))
                        end
                    end
                end
            end)
        end
    end
end)

-- [[ LÓGICA DE STATUS ]]
spawn(function()
    while task.wait(1) do
        if _G.AutoStats then
            game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("AddPoint", "Melee", 1)
        end
    end
end)

-- ANTI-AFK (Impedir que o jogo feche por inatividade)
game:GetService("Players").LocalPlayer.Idled:Connect(function()
    game:GetService("VirtualUser"):Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    task.wait(1)
    game:GetService("VirtualUser"):Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
end)
