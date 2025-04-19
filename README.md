-- Anti Ban & Anti Detected UI para Mini City (Mobile Friendly)

local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

-- Verifica se já existe para evitar recriação
if CoreGui:FindFirstChild("MiniCitySecureUI") then
    CoreGui.MiniCitySecureUI:Destroy()
end

-- Proteção contra deteção
local function proteger(obj)
    if syn and syn.protect_gui then
        syn.protect_gui(obj)
    elseif get_hidden_gui or gethui then
        local hiddenUI = get_hidden_gui and get_hidden_gui() or gethui()
        obj.Parent = hiddenUI
        return
    end
    obj.Parent = CoreGui
end

-- Criar UI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MiniCitySecureUI"
ScreenGui.ResetOnSpawn = false
proteger(ScreenGui)

-- Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 300, 0, 250)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -125)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.BackgroundTransparency = 0.2
MainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MainFrame.Parent = ScreenGui

-- Cantos arredondados
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = MainFrame

-- Título
local Title = Instance.new("TextLabel")
Title.Text = "Mini City - Painel Seguro"
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.Parent = MainFrame

-- Botão exemplo
local Button = Instance.new("TextButton")
Button.Size = UDim2.new(0.8, 0, 0, 40)
Button.Position = UDim2.new(0.1, 0, 0.4, 0)
Button.Text = "Função Segura"
Button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
Button.TextColor3 = Color3.fromRGB(255, 255, 255)
Button.Font = Enum.Font.Gotham
Button.TextSize = 16
Button.Parent = MainFrame

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 8)
btnCorner.Parent = Button

Button.MouseButton1Click:Connect(function()
    print("Função segura ativada")
end)

-- Área de texto para digitar o nick
local NickBox = Instance.new("TextBox")
NickBox.Size = UDim2.new(0.8, 0, 0, 35)
NickBox.Position = UDim2.new(0.1, 0, 0.65, 0)
NickBox.PlaceholderText = "Digite parte do nick"
NickBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
NickBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NickBox.Font = Enum.Font.Gotham
NickBox.TextSize = 16
NickBox.ClearTextOnFocus = false
NickBox.Parent = MainFrame

local boxCorner = Instance.new("UICorner")
boxCorner.CornerRadius = UDim.new(0, 8)
boxCorner.Parent = NickBox

-- Botão de teleportar
local TeleportButton = Instance.new("TextButton")
TeleportButton.Size = UDim2.new(0.8, 0, 0, 35)
TeleportButton.Position = UDim2.new(0.1, 0, 0.82, 0)
TeleportButton.Text = "Teleportar para jogador"
TeleportButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
TeleportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TeleportButton.Font = Enum.Font.Gotham
TeleportButton.TextSize = 16
TeleportButton.Parent = MainFrame

local tpBtnCorner = Instance.new("UICorner")
tpBtnCorner.CornerRadius = UDim.new(0, 8)
tpBtnCorner.Parent = TeleportButton

-- Função de teleportar
TeleportButton.MouseButton1Click:Connect(function()
    local textoDigitado = NickBox.Text:lower()
    if textoDigitado == "" then return end

    local alvoEncontrado
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Name:lower():find(textoDigitado) then
            alvoEncontrado = p
            break
        end
    end

    if alvoEncontrado and alvoEncontrado.Character and alvoEncontrado.Character:FindFirstChild("HumanoidRootPart") then
        player.Character:MoveTo(alvoEncontrado.Character.HumanoidRootPart.Position + Vector3.new(0, 3, 0))
    else
        print("Jogador não encontrado ou sem personagem.")
    end
end)

-- Tornar arrastável
local dragging, dragInput, dragStart, startPos

MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

MainFrame.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)
