-- Tanjiro Hub | Kaitun Edition
-- Script para Blox Fruits feito por ChatGPT

-- Carregar a UI
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Tanjiro Hub | Kaitun Mode", "BloodTheme")

-- Variáveis
getgenv().AutoFarm = false
getgenv().AutoQuest = false
getgenv().SelectedWeapon = "Melee" -- Você pode mudar para "Sword", "Gun", "Fruit"

-- Serviços
local player = game.Players.LocalPlayer
local rs = game:GetService("ReplicatedStorage")
local ws = game:GetService("Workspace")

-- Função para pegar arma/fruta
function EquipWeapon(ToolName)
    if player.Backpack:FindFirstChild(ToolName) then
        player.Character.Humanoid:EquipTool(player.Backpack[ToolName])
    end
end

-- Função para encontrar inimigo certo
function GetEnemy()
    local enemies = ws.Enemies:GetChildren()
    for i,v in pairs(enemies) do
        if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
            if v.Name:match(EnemyName) then
                return v
            end
        end
    end
    return nil
end

-- Função para Start AutoFarm
function StartFarm()
    spawn(function()
        while getgenv().AutoFarm do
            pcall(function()
                EquipWeapon(getgenv().SelectedWeapon)
                
                -- Detecção automática de missões e inimigos
                SetQuest()

                local enemy = GetEnemy()
                if enemy then
                    repeat
                        player.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0,5,0)
                        rs.Remotes.CommF_:InvokeServer("Attack")
                        wait(0.1)
                    until enemy.Humanoid.Health <= 0 or not getgenv().AutoFarm
                else
                    wait(1)
                end
            end)
            wait(0.5)
        end
    end)
end

-- Função para detectar e aceitar a missão correta
function SetQuest()
    local level = player.Data.Level.Value
    if level <= 10 then
        QuestName = "BanditQuest1"
        EnemyName = "Bandit"
        QuestPos = CFrame.new(1060,16,1547)
    elseif level <= 20 then
        QuestName = "BanditQuest2"
        EnemyName = "Brute"
        QuestPos = CFrame.new(1060,16,1547)
    -- Aqui você continua adicionando mais quests conforme o level
    end

    -- Aceitar a missão
    rs.Remotes.CommF_:InvokeServer("StartQuest", QuestName, 1)
end

-- Anti-AFK
pcall(function()
    local vu = game:GetService("VirtualUser")
    game:GetService("Players").LocalPlayer.Idled:connect(function()
        vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        wait(1)
        vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    end)
end)

-- Criação das abas
local FarmTab = Window:NewTab("Auto Farm")
local FarmSection = FarmTab:NewSection("Farm")

FarmSection:NewToggle("Auto Farm Kaitun", "Farm Inteligente", function(state)
    getgenv().AutoFarm = state
    if state then
        StartFarm()
    end
end)

FarmSection:NewDropdown("Arma/Fruit", "Selecionar Arma ou Fruta", {"Melee", "Sword", "Gun", "Fruit"}, function(currentOption)
    getgenv().SelectedWeapon = currentOption
end)
