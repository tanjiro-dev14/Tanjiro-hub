-- Tanjiro Hub | Estilo Redz (Azul)
-- Feito por ChatGPT para Blox Fruits

-- UI Library
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()

local Window = Library.CreateLib("Tanjiro Hub", "Sentinel") -- Tema Azul

-- Variáveis
getgenv().AutoFarm = false
getgenv().AutoFarmBoss = false

-- Serviços
local plr = game.Players.LocalPlayer
local rs = game:GetService("ReplicatedStorage")
local ws = game:GetService("Workspace")

-- Funções
function EquipWeapon()
    for i,v in pairs(plr.Backpack:GetChildren()) do
        if v:IsA("Tool") then
            plr.Character.Humanoid:EquipTool(v)
            break
        end
    end
end

function GetClosestEnemy()
    local nearest, dist = nil, math.huge
    for i,v in pairs(ws.Enemies:GetChildren()) do
        if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
            local magnitude = (plr.Character.HumanoidRootPart.Position - v.HumanoidRootPart.Position).magnitude
            if magnitude < dist then
                nearest = v
                dist = magnitude
            end
        end
    end
    return nearest
end

function AutoFarm()
    while getgenv().AutoFarm do
        pcall(function()
            EquipWeapon()
            local enemy = GetClosestEnemy()
            if enemy then
                plr.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0,5,0)
                rs.Remotes.CommF_:InvokeServer("Attack")
            end
        end)
        wait(0.2)
    end
end

-- ABA 1: Auto Farm
local FarmTab = Window:NewTab("Auto Farm")
local FarmSection = FarmTab:NewSection("Main Farm")

FarmSection:NewToggle("Ativar AutoFarm", "Farmar automaticamente", function(state)
    getgenv().AutoFarm = state
    if state then
        AutoFarm()
    end
end)

-- ABA 2: Teleports Simples
local TeleportTab = Window:NewTab("Teleports")
local TeleportSection = TeleportTab:NewSection("Ilhas")

TeleportSection:NewButton("Ir para Starter Island", "Teleporta", function()
    plr.Character.HumanoidRootPart.CFrame = CFrame.new(-260, 7, 1550)
end)

TeleportSection:NewButton("Ir para Jungle Island", "Teleporta", function()
    plr.Character.HumanoidRootPart.CFrame = CFrame.new(-1250, 11, 350)
end)

TeleportSection:NewButton("Ir para Marine Fortress", "Teleporta", function()
    plr.Character.HumanoidRootPart.CFrame = CFrame.new(-4500, 20, 4300)
end)

-- Anti-AFK
pcall(function()
    local vu = game:GetService("VirtualUser")
    plr.Idled:connect(function()
        vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        wait(1)
        vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    end)
end)
