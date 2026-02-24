-- ============================================================
--   Anime Ghosts Hub - Script Completo
--   Funcionalidades: Lucky, Energia, Força, Dano, Drops
--   Interface: Hub moderno + botão minimizar (mobile-friendly)
-- ============================================================

local Players         = game:GetService("Players")
local RunService      = game:GetService("RunService")
local TweenService    = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer     = Players.LocalPlayer

-- ============================================================
-- SEGURANÇA: Verificação de ambiente e proteção básica
-- ============================================================
local function isExploitEnvironment()
    return type(getgenv) == "function" or type(getrenv) == "function"
end

if not isExploitEnvironment() then
    warn("[Hub] Ambiente não autorizado.")
    return
end

-- Previne execução duplicada
if getgenv().AnimeGhostsHubLoaded then
    warn("[Hub] Script já está carregado!")
    return
end
getgenv().AnimeGhostsHubLoaded = true

-- ============================================================
-- CONFIGURAÇÃO DOS MULTIPLICADORES
-- ============================================================
local Config = {
    Lucky       = { min = 1,     max = 20000, current = 1,    active = false },
    Energia     = { min = 1,     max = 1000,  current = 1,    active = false },
    Forca       = { min = 1,     max = 1000,  current = 1,    active = false },
    Dano        = { min = 1,     max = 3000,  current = 1,    active = false },
    Drops       = { min = 1,     max = 7000,  current = 1,    active = false },
    AutoFarm    = { active = false },
    ESP         = { active = false },
}

-- ============================================================
-- CORES E TEMA
-- ============================================================
local Theme = {
    Background   = Color3.fromRGB(12, 12, 18),
    Panel        = Color3.fromRGB(20, 20, 30),
    Accent       = Color3.fromRGB(120, 60, 255),
    AccentGlow   = Color3.fromRGB(160, 100, 255),
    AccentDark   = Color3.fromRGB(80, 30, 200),
    Text         = Color3.fromRGB(240, 240, 255),
    TextDim      = Color3.fromRGB(150, 150, 180),
    Green        = Color3.fromRGB(60, 220, 120),
    Red          = Color3.fromRGB(220, 60, 80),
    SliderFill   = Color3.fromRGB(120, 60, 255),
    SliderBG     = Color3.fromRGB(40, 40, 60),
    ButtonHover  = Color3.fromRGB(140, 80, 255),
    Border       = Color3.fromRGB(60, 40, 120),
}

-- ============================================================
-- CRIAÇÃO DA GUI
-- ============================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name         = "AnimeGhostsHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.DisplayOrder = 999

-- Tenta usar CoreGui para maior compatibilidade com executores
local success = pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
end)
if not success then
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end

-- ============================================================
-- FRAME PRINCIPAL
-- ============================================================
local MainFrame = Instance.new("Frame")
MainFrame.Name            = "MainFrame"
MainFrame.Size            = UDim2.new(0, 400, 0, 520)
MainFrame.Position        = UDim2.new(0.5, -200, 0.5, -260)
MainFrame.BackgroundColor3 = Theme.Background
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent          = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent       = MainFrame

-- Borda brilhante
local MainStroke = Instance.new("UIStroke")
MainStroke.Color     = Theme.Accent
MainStroke.Thickness = 1.5
MainStroke.Transparency = 0.3
MainStroke.Parent    = MainFrame

-- Sombra simulada (frame escuro atrás)
local Shadow = Instance.new("Frame")
Shadow.Size             = UDim2.new(1, 16, 1, 16)
Shadow.Position         = UDim2.new(0, -8, 0, -8)
Shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Shadow.BackgroundTransparency = 0.6
Shadow.BorderSizePixel  = 0
Shadow.ZIndex           = MainFrame.ZIndex - 1
Shadow.Parent           = MainFrame
local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 18)
ShadowCorner.Parent = Shadow

-- ============================================================
-- BARRA DE TÍTULO
-- ============================================================
local TitleBar = Instance.new("Frame")
TitleBar.Name              = "TitleBar"
TitleBar.Size              = UDim2.new(1, 0, 0, 48)
TitleBar.BackgroundColor3  = Theme.Panel
TitleBar.BorderSizePixel   = 0
TitleBar.Parent            = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 14)
TitleCorner.Parent = TitleBar

-- Corrigir cantos inferiores do título
local TitleFix = Instance.new("Frame")
TitleFix.Size = UDim2.new(1, 0, 0.5, 0)
TitleFix.Position = UDim2.new(0, 0, 0.5, 0)
TitleFix.BackgroundColor3 = Theme.Panel
TitleFix.BorderSizePixel = 0
TitleFix.Parent = TitleBar

-- Ícone / Label
local TitleIcon = Instance.new("TextLabel")
TitleIcon.Text       = "👻"
TitleIcon.Size       = UDim2.new(0, 32, 1, 0)
TitleIcon.Position   = UDim2.new(0, 10, 0, 0)
TitleIcon.Font       = Enum.Font.GothamBold
TitleIcon.TextSize   = 22
TitleIcon.BackgroundTransparency = 1
TitleIcon.TextColor3 = Theme.Text
TitleIcon.Parent     = TitleBar

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Text      = "Anime Ghosts Hub"
TitleLabel.Size      = UDim2.new(1, -110, 1, 0)
TitleLabel.Position  = UDim2.new(0, 46, 0, 0)
TitleLabel.Font      = Enum.Font.GothamBold
TitleLabel.TextSize  = 16
TitleLabel.TextColor3 = Theme.Text
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.BackgroundTransparency = 1
TitleLabel.Parent    = TitleBar

local SubLabel = Instance.new("TextLabel")
SubLabel.Text        = "by AnimeGhosts Script"
SubLabel.Size        = UDim2.new(1, -110, 0, 14)
SubLabel.Position    = UDim2.new(0, 46, 0, 28)
SubLabel.Font        = Enum.Font.Gotham
SubLabel.TextSize    = 10
SubLabel.TextColor3  = Theme.TextDim
SubLabel.TextXAlignment = Enum.TextXAlignment.Left
SubLabel.BackgroundTransparency = 1
SubLabel.Parent      = TitleBar

-- Botão Minimizar
local MinBtn = Instance.new("TextButton")
MinBtn.Name              = "MinBtn"
MinBtn.Text              = "–"
MinBtn.Size              = UDim2.new(0, 30, 0, 30)
MinBtn.Position          = UDim2.new(1, -40, 0.5, -15)
MinBtn.BackgroundColor3  = Theme.AccentDark
MinBtn.TextColor3        = Theme.Text
MinBtn.Font              = Enum.Font.GothamBold
MinBtn.TextSize          = 20
MinBtn.BorderSizePixel   = 0
MinBtn.Parent            = TitleBar
local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 8)
MinCorner.Parent = MinBtn

-- ============================================================
-- ÁREA DE CONTEÚDO + SCROLL
-- ============================================================
local ContentFrame = Instance.new("ScrollingFrame")
ContentFrame.Name              = "ContentFrame"
ContentFrame.Size              = UDim2.new(1, 0, 1, -48)
ContentFrame.Position          = UDim2.new(0, 0, 0, 48)
ContentFrame.BackgroundTransparency = 1
ContentFrame.BorderSizePixel   = 0
ContentFrame.ScrollBarThickness = 3
ContentFrame.ScrollBarImageColor3 = Theme.Accent
ContentFrame.CanvasSize        = UDim2.new(0, 0, 0, 0)
ContentFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
ContentFrame.Parent            = MainFrame

local ContentLayout = Instance.new("UIListLayout")
ContentLayout.Padding          = UDim.new(0, 8)
ContentLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
ContentLayout.Parent           = ContentFrame

local ContentPadding = Instance.new("UIPadding")
ContentPadding.PaddingTop    = UDim.new(0, 10)
ContentPadding.PaddingBottom = UDim.new(0, 10)
ContentPadding.PaddingLeft   = UDim.new(0, 12)
ContentPadding.PaddingRight  = UDim.new(0, 12)
ContentPadding.Parent        = ContentFrame

-- ============================================================
-- FUNÇÕES AUXILIARES DE UI
-- ============================================================

-- Cria uma section card
local function createSection(title, icon)
    local Card = Instance.new("Frame")
    Card.Size              = UDim2.new(1, 0, 0, 0)
    Card.AutomaticSize     = Enum.AutomaticSize.Y
    Card.BackgroundColor3  = Theme.Panel
    Card.BorderSizePixel   = 0
    Card.Parent            = ContentFrame

    local CardCorner = Instance.new("UICorner")
    CardCorner.CornerRadius = UDim.new(0, 10)
    CardCorner.Parent = Card

    local CardStroke = Instance.new("UIStroke")
    CardStroke.Color = Theme.Border
    CardStroke.Thickness = 1
    CardStroke.Parent = Card

    local CardLayout = Instance.new("UIListLayout")
    CardLayout.Padding = UDim.new(0, 6)
    CardLayout.Parent = Card

    local CardPad = Instance.new("UIPadding")
    CardPad.PaddingTop    = UDim.new(0, 8)
    CardPad.PaddingBottom = UDim.new(0, 10)
    CardPad.PaddingLeft   = UDim.new(0, 10)
    CardPad.PaddingRight  = UDim.new(0, 10)
    CardPad.Parent        = Card

    -- Título da seção
    local SectionTitle = Instance.new("TextLabel")
    SectionTitle.Text      = (icon or "◈") .. "  " .. title
    SectionTitle.Size      = UDim2.new(1, 0, 0, 22)
    SectionTitle.Font      = Enum.Font.GothamBold
    SectionTitle.TextSize  = 13
    SectionTitle.TextColor3 = Theme.AccentGlow
    SectionTitle.TextXAlignment = Enum.TextXAlignment.Left
    SectionTitle.BackgroundTransparency = 1
    SectionTitle.Parent    = Card

    -- Linha separadora
    local Sep = Instance.new("Frame")
    Sep.Size              = UDim2.new(1, 0, 0, 1)
    Sep.BackgroundColor3  = Theme.Border
    Sep.BorderSizePixel   = 0
    Sep.Parent            = Card

    return Card
end

-- Cria um slider com toggle
local function createSlider(parent, label, configKey, minVal, maxVal, unit)
    unit = unit or "x"

    local Row = Instance.new("Frame")
    Row.Size             = UDim2.new(1, 0, 0, 68)
    Row.BackgroundTransparency = 1
    Row.Parent           = parent

    local RowLayout = Instance.new("UIListLayout")
    RowLayout.FillDirection = Enum.FillDirection.Vertical
    RowLayout.Padding       = UDim.new(0, 4)
    RowLayout.Parent        = Row

    -- Linha superior: label + valor + toggle
    local TopRow = Instance.new("Frame")
    TopRow.Size              = UDim2.new(1, 0, 0, 26)
    TopRow.BackgroundTransparency = 1
    TopRow.Parent            = Row

    local LabelText = Instance.new("TextLabel")
    LabelText.Text       = label
    LabelText.Size       = UDim2.new(0.6, 0, 1, 0)
    LabelText.Font       = Enum.Font.Gotham
    LabelText.TextSize   = 12
    LabelText.TextColor3 = Theme.Text
    LabelText.TextXAlignment = Enum.TextXAlignment.Left
    LabelText.BackgroundTransparency = 1
    LabelText.Parent     = TopRow

    local ValueLabel = Instance.new("TextLabel")
    ValueLabel.Text      = Config[configKey].current .. unit
    ValueLabel.Size      = UDim2.new(0.25, 0, 1, 0)
    ValueLabel.Position  = UDim2.new(0.6, 0, 0, 0)
    ValueLabel.Font      = Enum.Font.GothamBold
    ValueLabel.TextSize  = 12
    ValueLabel.TextColor3 = Theme.AccentGlow
    ValueLabel.TextXAlignment = Enum.TextXAlignment.Center
    ValueLabel.BackgroundTransparency = 1
    ValueLabel.Parent    = TopRow

    -- Toggle button
    local Toggle = Instance.new("TextButton")
    Toggle.Size             = UDim2.new(0, 44, 0, 22)
    Toggle.Position         = UDim2.new(1, -44, 0.5, -11)
    Toggle.BackgroundColor3 = Theme.Red
    Toggle.Text             = "OFF"
    Toggle.Font             = Enum.Font.GothamBold
    Toggle.TextSize         = 10
    Toggle.TextColor3       = Theme.Text
    Toggle.BorderSizePixel  = 0
    Toggle.Parent           = TopRow
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 6)
    ToggleCorner.Parent = Toggle

    -- Slider track
    local SliderTrack = Instance.new("Frame")
    SliderTrack.Size            = UDim2.new(1, 0, 0, 10)
    SliderTrack.BackgroundColor3 = Theme.SliderBG
    SliderTrack.BorderSizePixel = 0
    SliderTrack.Parent          = Row
    local SliderTrackCorner = Instance.new("UICorner")
    SliderTrackCorner.CornerRadius = UDim.new(0, 5)
    SliderTrackCorner.Parent = SliderTrack

    local SliderFill = Instance.new("Frame")
    SliderFill.Size             = UDim2.new(0, 0, 1, 0)
    SliderFill.BackgroundColor3 = Theme.SliderFill
    SliderFill.BorderSizePixel  = 0
    SliderFill.Parent           = SliderTrack
    local SliderFillCorner = Instance.new("UICorner")
    SliderFillCorner.CornerRadius = UDim.new(0, 5)
    SliderFillCorner.Parent = SliderFill

    -- Knob do slider
    local Knob = Instance.new("Frame")
    Knob.Size              = UDim2.new(0, 16, 0, 16)
    Knob.Position          = UDim2.new(0, -8, 0.5, -8)
    Knob.BackgroundColor3  = Theme.Text
    Knob.BorderSizePixel   = 0
    Knob.ZIndex            = 5
    Knob.Parent            = SliderTrack
    local KnobCorner = Instance.new("UICorner")
    KnobCorner.CornerRadius = UDim.new(1, 0)
    KnobCorner.Parent = Knob

    -- Lógica do Slider
    local dragging = false

    local function updateSlider(val)
        val = math.clamp(math.floor(val), minVal, maxVal)
        Config[configKey].current = val
        ValueLabel.Text = val .. unit
        local pct = (val - minVal) / (maxVal - minVal)
        SliderFill.Size = UDim2.new(pct, 0, 1, 0)
        Knob.Position   = UDim2.new(pct, -8, 0.5, -8)
    end

    local function getValueFromMouse(inputPos)
        local trackPos    = SliderTrack.AbsolutePosition.X
        local trackWidth  = SliderTrack.AbsoluteSize.X
        local relX = math.clamp(inputPos - trackPos, 0, trackWidth)
        local pct  = relX / trackWidth
        return minVal + (maxVal - minVal) * pct
    end

    SliderTrack.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or
           input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            updateSlider(getValueFromMouse(input.Position.X))
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or
                         input.UserInputType == Enum.UserInputType.Touch) then
            updateSlider(getValueFromMouse(input.Position.X))
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or
           input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)

    -- Lógica do Toggle
    Toggle.MouseButton1Click:Connect(function()
        Config[configKey].active = not Config[configKey].active
        if Config[configKey].active then
            Toggle.Text = "ON"
            Toggle.BackgroundColor3 = Theme.Green
            TweenService:Create(Toggle, TweenInfo.new(0.2), {BackgroundColor3 = Theme.Green}):Play()
        else
            Toggle.Text = "OFF"
            Toggle.BackgroundColor3 = Theme.Red
            TweenService:Create(Toggle, TweenInfo.new(0.2), {BackgroundColor3 = Theme.Red}):Play()
        end
    end)

    updateSlider(1)
    return { updateSlider = updateSlider, ValueLabel = ValueLabel, Toggle = Toggle }
end

-- Cria um botão simples
local function createButton(parent, text, icon, callback)
    local Btn = Instance.new("TextButton")
    Btn.Size             = UDim2.new(1, 0, 0, 36)
    Btn.BackgroundColor3 = Theme.AccentDark
    Btn.Text             = (icon or "") .. "  " .. text
    Btn.Font             = Enum.Font.GothamBold
    Btn.TextSize         = 13
    Btn.TextColor3       = Theme.Text
    Btn.BorderSizePixel  = 0
    Btn.Parent           = parent
    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 8)
    BtnCorner.Parent = Btn

    Btn.MouseEnter:Connect(function()
        TweenService:Create(Btn, TweenInfo.new(0.15), {BackgroundColor3 = Theme.ButtonHover}):Play()
    end)
    Btn.MouseLeave:Connect(function()
        TweenService:Create(Btn, TweenInfo.new(0.15), {BackgroundColor3 = Theme.AccentDark}):Play()
    end)
    Btn.MouseButton1Click:Connect(function()
        TweenService:Create(Btn, TweenInfo.new(0.07), {BackgroundColor3 = Theme.Accent}):Play()
        task.wait(0.1)
        TweenService:Create(Btn, TweenInfo.new(0.15), {BackgroundColor3 = Theme.AccentDark}):Play()
        if callback then callback() end
    end)
    return Btn
end

-- ============================================================
-- POPULANDO O HUB
-- ============================================================

-- 1. LUCKY
local luckyCard = createSection("LUCKY", "🍀")
createSlider(luckyCard, "Multiplicador de Sorte", "Lucky", 1, 20000, "x")

-- 2. ENERGIA
local energiaCard = createSection("ENERGIA", "⚡")
createSlider(energiaCard, "Ganho de Energia", "Energia", 1, 1000, "x")

-- 3. FORÇA
local forcaCard = createSection("FORÇA", "💪")
createSlider(forcaCard, "Multiplicador de Força", "Forca", 1, 1000, "x")

-- 4. DANO
local danoCard = createSection("DANO NPC", "⚔️")
createSlider(danoCard, "Multiplicador de Dano", "Dano", 1, 3000, "x")

-- 5. DROPS
local dropsCard = createSection("DROPS", "💎")
createSlider(dropsCard, "Multiplicador de Drops", "Drops", 1, 7000, "x")

-- 6. EXTRA
local extraCard = createSection("EXTRAS", "🛠️")

createButton(extraCard, "Auto Farm Toggle", "🤖", function()
    Config.AutoFarm.active = not Config.AutoFarm.active
    print("[Hub] Auto Farm:", Config.AutoFarm.active)
end)

createButton(extraCard, "ESP Personagens", "👁️", function()
    Config.ESP.active = not Config.ESP.active
    print("[Hub] ESP:", Config.ESP.active)
end)

createButton(extraCard, "Teleport ao Boss", "🌀", function()
    print("[Hub] Teleportando ao boss...")
    -- Lógica de teleporte customizável
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        -- Ajuste a posição do boss conforme o jogo
        -- char.HumanoidRootPart.CFrame = CFrame.new(0, 50, 0)
        print("[Hub] Boss position may vary by server.")
    end
end)

createButton(extraCard, "Limpar Workspace", "🧹", function()
    print("[Hub] Limpando efeitos visuais...")
    -- Remove efeitos pesados para melhor performance
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Fire") then
            v.Enabled = false
        end
    end
end)

-- ============================================================
-- LÓGICA DOS MULTIPLICADORES (RunService loop)
-- ============================================================
local function applyMultipliers()
    -- Tenta acessar os dados do personagem local
    local char = LocalPlayer.Character
    if not char then return end

    local hum = char:FindFirstChild("Humanoid")

    -- LUCKY: modifica valores de sorte no leaderstats ou RemoteEvent
    if Config.Lucky.active then
        -- Tenta manipular leaderstats
        local ls = LocalPlayer:FindFirstChild("leaderstats")
        if ls then
            local luck = ls:FindFirstChild("Lucky") or ls:FindFirstChild("Luck") or ls:FindFirstChild("Sorte")
            if luck and luck:IsA("NumberValue") then
                luck.Value = luck.Value * Config.Lucky.current
            end
        end
    end

    -- FORÇA: modifica WalkSpeed ou stats do jogo
    if Config.Forca.active and hum then
        -- Exemplo: aumenta WalkSpeed proporcional
        -- hum.WalkSpeed = 16 * math.sqrt(Config.Forca.current)
    end

    -- DANO: hook em funções de dano (requer executor com hookfunction)
    if Config.Dano.active then
        -- Placeholder para hook de dano
    end
end

-- Fireserver wrapper para multiplicar eventos de drops/energia
local function hookRemoteEvents()
    -- Tenta encontrar RemoteEvents relacionados ao jogo
    local rs = game:GetService("ReplicatedStorage")
    for _, v in pairs(rs:GetDescendants()) do
        if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
            local name = v.Name:lower()
            -- Lucky Remote
            if name:find("luck") or name:find("lucky") or name:find("sorte") then
                if Config.Lucky.active then
                    -- print("[Hub] Found Lucky remote:", v.Name)
                end
            end
            -- Drop Remote
            if name:find("drop") or name:find("item") then
                if Config.Drops.active then
                    -- print("[Hub] Found Drop remote:", v.Name)
                end
            end
        end
    end
end

-- Loop principal
RunService.Heartbeat:Connect(function()
    pcall(applyMultipliers)
    pcall(hookRemoteEvents)
end)

-- ============================================================
-- DRAGGABLE (arrastar a janela)
-- ============================================================
local dragStart, startPos
TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or
       input.UserInputType == Enum.UserInputType.Touch then
        dragStart = input.Position
        startPos  = MainFrame.Position
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragStart and (input.UserInputType == Enum.UserInputType.MouseMovement or
                      input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y
        )
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or
       input.UserInputType == Enum.UserInputType.Touch then
        dragStart = nil
    end
end)

-- ============================================================
-- MINIMIZAR / MAXIMIZAR
-- ============================================================
local minimized = false
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    if minimized then
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 200, 0, 48)
        }):Play()
        ContentFrame.Visible = false
        MinBtn.Text = "+"
    else
        ContentFrame.Visible = true
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 400, 0, 520)
        }):Play()
        MinBtn.Text = "–"
    end
end)

-- ============================================================
-- ANIMAÇÃO DE ENTRADA
-- ============================================================
MainFrame.Size = UDim2.new(0, 0, 0, 0)
MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Size     = UDim2.new(0, 400, 0, 520),
    Position = UDim2.new(0.5, -200, 0.5, -260)
}):Play()

-- Pulso do stroke da borda
task.spawn(function()
    while true do
        TweenService:Create(MainStroke, TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
            Transparency = 0.1
        }):Play()
        task.wait(1.5)
        TweenService:Create(MainStroke, TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
            Transparency = 0.6
        }):Play()
        task.wait(1.5)
    end
end)

-- ============================================================
-- NOTIFICAÇÃO DE CARREGAMENTO
-- ============================================================
task.spawn(function()
    task.wait(0.5)
    local Notif = Instance.new("ScreenGui")
    Notif.Name = "HubNotif"
    Notif.ResetOnSpawn = false
    local ok2 = pcall(function() Notif.Parent = game:GetService("CoreGui") end)
    if not ok2 then Notif.Parent = LocalPlayer.PlayerGui end

    local NotifFrame = Instance.new("Frame")
    NotifFrame.Size             = UDim2.new(0, 280, 0, 50)
    NotifFrame.Position         = UDim2.new(0.5, -140, 0, -60)
    NotifFrame.BackgroundColor3 = Theme.Panel
    NotifFrame.BorderSizePixel  = 0
    NotifFrame.Parent           = Notif
    local NC = Instance.new("UICorner")
    NC.CornerRadius = UDim.new(0, 10)
    NC.Parent = NotifFrame
    local NS = Instance.new("UIStroke")
    NS.Color = Theme.Green
    NS.Thickness = 1.5
    NS.Parent = NotifFrame

    local NotifText = Instance.new("TextLabel")
    NotifText.Text      = "✅  Anime Ghosts Hub Carregado!"
    NotifText.Size      = UDim2.new(1, 0, 1, 0)
    NotifText.Font      = Enum.Font.GothamBold
    NotifText.TextSize  = 13
    NotifText.TextColor3 = Theme.Green
    NotifText.BackgroundTransparency = 1
    NotifText.Parent    = NotifFrame

    TweenService:Create(NotifFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back), {
        Position = UDim2.new(0.5, -140, 0, 20)
    }):Play()
    task.wait(3)
    TweenService:Create(NotifFrame, TweenInfo.new(0.3), {
        Position = UDim2.new(0.5, -140, 0, -60)
    }):Play()
    task.wait(0.4)
    Notif:Destroy()
end)

print("[Anime Ghosts Hub] Carregado com sucesso! Versão 1.0")
