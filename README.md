-- Tanjiro Hub | Supreme Blue Edition
-- Feito por ChatGPT para Blox Fruits

-- Criar Toggle Gui
local ScreenGui = Instance.new("ScreenGui")
local OpenButton = Instance.new("TextButton")

ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "TanjiroToggleGui"

OpenButton.Parent = ScreenGui
OpenButton.Size = UDim2.new(0, 50, 0, 50)
OpenButton.Position = UDim2.new(0, 10, 0, 10)
OpenButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
OpenButton.Text = "☰"
OpenButton.Font = Enum.Font.SourceSansBold
OpenButton.TextColor3 = Color3.fromRGB(255,255,255)
OpenButton.TextSize = 30

-- Variável do Hub Principal
local HubVisible = false
local MainGui

-- Função para Criar o Hub
function CreateHub()
    -- Carregar Library Azul
    local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
    MainGui = Library.CreateLib("Tanjiro Hub | Blue Mode", "Sentinel") -- Tema Azul

    -- Variáveis Globais
    getgenv().AutoFarm = false
    getgenv().SelectedWeapon = "Melee"

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
        local closest, dist = nil, math.huge
        for _, enemy in pairs(ws.Enemies:GetChildren()) do
            if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                local mag = (plr.Character.HumanoidRootPart.Position - enemy.HumanoidRootPart.Position).Magnitude
                if mag < dist then
                    closest = enemy
                    dist = mag
                end
            end
        end
        return closest
    end

    function StartAutoFarm()
        while getgenv().AutoFarm do
            pcall(function()
                EquipWeapon()
                local enemy = GetClosestEnemy()
                if enemy then
                    plr.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0,5,0)
                    rs.Remotes.CommF_:InvokeServer("Attack")
                end
            end)
            task.wait(0.3)
        end
    end

    -- Tabs
    local FarmTab = MainGui:NewTab("Auto Farm")
    local TeleportTab = MainGui:NewTab("Teleports")
    local VisualTab = MainGui:NewTab("Visual (ESP)")
    local CreditsTab = MainGui:NewTab("Créditos")

    -- Seções
    local FarmSection = FarmTab:NewSection("Farm")
    FarmSection:NewToggle("Ativar AutoFarm", "Farme automaticamente", function(state)
        getgenv().AutoFarm = state
        if state then
            StartAutoFarm()
        end
    end)

    local TeleportSection = TeleportTab:NewSection("Teleports rápidos")
    TeleportSection:NewButton("Ir para Starter Island", "TP", function()
        plr.Character.HumanoidRootPart.CFrame = CFrame.new(-260, 7, 1550)
    end)
    TeleportSection:NewButton("Ir para Jungle Island", "TP", function()
        plr.Character.HumanoidRootPart.CFrame = CFrame.new(-1249, 15, 340)
    end)
    TeleportSection:NewButton("Ir para Marine Fortress", "TP", function()
        plr.Character.HumanoidRootPart.CFrame = CFrame.new(-4500, 20, 4300)
    end)

    local VisualSection = VisualTab:NewSection("ESP")
    VisualSection:NewButton("Ativar ESP de Inimigos", "Ver NPCs através das paredes", function()
        for i,v in pairs(ws.Enemies:GetChildren()) do
            if v:FindFirstChild("HumanoidRootPart") then
                local esp = Instance.new("BillboardGui", v.HumanoidRootPart)
                esp.Size = UDim2.new(0,100,0,40)
                esp.AlwaysOnTop = true
                local text = Instance.new("TextLabel", esp)
                text.Size = UDim2.new(1,0,1,0)
                text.BackgroundTransparency = 1
                text.Text = v.Name
                text.TextColor3 = Color3.fromRGB(0,255,255)
                text.TextStrokeTransparency = 0
            end
        end
    end)

    local CreditsSection = CreditsTab:NewSection("Feito por ChatGPT")
    CreditsSection:NewLabel("Tanjiro Hub - Supreme Blue Edition")
end

-- Sistema de abrir/fechar
OpenButton.MouseButton1Click:Connect(function()
    if not HubVisible then
        if not MainGui then
            CreateHub()
        else
            for _,v in pairs(game.CoreGui:GetChildren()) do
                if v.Name == "Tanjiro Hub | Blue Mode" then
                    v.Enabled = true
                end
            end
        end
        HubVisible = true
    else
        for _,v in pairs(game.CoreGui:GetChildren()) do
            if v.Name == "Tanjiro Hub | Blue Mode" then
                v.Enabled = false
            end
        end
        HubVisible = false
    end
end)

-- Anti-AFK
pcall(function()
    local vu = game:GetService("VirtualUser")
    game:GetService("Players").LocalPlayer.Idled:connect(function()
        vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        wait(1)
        vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    end)
end)
