-- Tanjiro Hub | Simples e Funcional
-- Feito por ChatGPT para Blox Fruits

-- Variáveis
getgenv().AutoFarm = false

-- Serviços
local plr = game.Players.LocalPlayer
local rs = game:GetService("ReplicatedStorage")
local ws = game:GetService("Workspace")

-- Funções
function EquipBestWeapon()
    local tool = plr.Backpack:FindFirstChildOfClass("Tool")
    if tool then
        plr.Character.Humanoid:EquipTool(tool)
    end
end

function GetNearestEnemy()
    local closest, distance = nil, math.huge
    for _,enemy in pairs(ws.Enemies:GetChildren()) do
        if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
            local dist = (plr.Character.HumanoidRootPart.Position - enemy.HumanoidRootPart.Position).magnitude
            if dist < distance then
                closest = enemy
                distance = dist
            end
        end
    end
    return closest
end

function StartAutoFarm()
    while getgenv().AutoFarm do
        pcall(function()
            EquipBestWeapon()
            local enemy = GetNearestEnemy()
            if enemy then
                plr.Character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0,5,0)
                rs.Remotes.CommF_:InvokeServer("Attack")
            end
        end)
        task.wait(0.3)
    end
end

-- Interface Básica
local ScreenGui = Instance.new("ScreenGui")
local ToggleButton = Instance.new("TextButton")

ScreenGui.Parent = game.CoreGui
ToggleButton.Parent = ScreenGui
ToggleButton.Position = UDim2.new(0, 50, 0, 100)
ToggleButton.Size = UDim2.new(0, 200, 0, 50)
ToggleButton.Text = "Tanjiro Hub: Ativar AutoFarm"
ToggleButton.BackgroundColor3 = Color3.fromRGB(200,50,50)
ToggleButton.TextColor3 = Color3.fromRGB(255,255,255)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 22

ToggleButton.MouseButton1Click:Connect(function()
    getgenv().AutoFarm = not getgenv().AutoFarm
    if getgenv().AutoFarm then
        ToggleButton.Text = "Tanjiro Hub: AutoFarm ON"
        StartAutoFarm()
    else
        ToggleButton.Text = "Tanjiro Hub: AutoFarm OFF"
    end
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
