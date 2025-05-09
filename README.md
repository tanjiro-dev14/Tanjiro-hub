-- ESP Simples com interface básica
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local ESPEnabled = true
local ESPObjects = {}

-- Função para criar uma linha de ESP
local function createESP(player)
    local box = Drawing.new("Square")
    box.Color = Color3.fromRGB(255, 255, 0)
    box.Thickness = 2
    box.Filled = false
    box.Visible = false

    local name = Drawing.new("Text")
    name.Size = 16
    name.Color = Color3.fromRGB(255, 255, 255)
    name.Center = true
    name.Outline = true
    name.Visible = false

    ESPObjects[player] = {Box = box, Name = name}
end

-- Atualiza os ESPs
RunService.RenderStepped:Connect(function()
    if not ESPEnabled then return end

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local esp = ESPObjects[player]
            if not esp then
                createESP(player)
                esp = ESPObjects[player]
            end

            local pos, onScreen = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if onScreen then
                local size = Vector2.new(40, 60)
                esp.Box.Size = size
                esp.Box.Position = Vector2.new(pos.X - size.X/2, pos.Y - size.Y/2)
                esp.Box.Visible = true

                esp.Name.Text = player.Name
                esp.Name.Position = Vector2.new(pos.X, pos.Y - size.Y/2 - 15)
                esp.Name.Visible = true
            else
                esp.Box.Visible = false
                esp.Name.Visible = false
            end
        elseif ESPObjects[player] then
            ESPObjects[player].Box.Visible = false
            ESPObjects[player].Name.Visible = false
        end
    end
end)

-- Remover ESPs ao sair
Players.PlayerRemoving:Connect(function(player)
    if ESPObjects[player] then
        ESPObjects[player].Box:Remove()
        ESPObjects[player].Name:Remove()
        ESPObjects[player] = nil
    end
end)

-- Interface simples para toggle (pressionar "J" ativa/desativa)
game:GetService("UserInputService").InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.J then
        ESPEnabled = not ESPEnabled
        print("ESP Ativado:", ESPEnabled)
    end
end)