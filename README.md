# REDZ-but-better                                                                                                                                                                                                                                             -- Biblioteca da Interface (Substitua com o link da sua biblioteca UI)
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/YourLibraryLink"))()  -- Substitua pelo link real da sua biblioteca de UI

-- Criando a Janela da Interface
local Window = Library:CreateWindow("RedzHub Melhorado")

-- Criando a aba de configurações
local SettingsTab = Window:CreateTab("Configurações")

-- Variáveis de controle
local autoClicking = false
local clickRate = 0.1  -- Taxa de clique ajustável (em segundos)
local attackDelay = 0.5  -- Delay entre os ataques

-- Função de Auto-Click
local function startAutoClick()
    local lastClickTime = 0
    while autoClicking do
        wait(0.05)  -- Intervalo de espera pequeno para evitar sobrecarga
        local currentTime = tick()
        if currentTime - lastClickTime >= clickRate then
            lastClickTime = currentTime
            local character = game.Players.LocalPlayer.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                -- Ataque automático
                for _, enemy in pairs(workspace:GetChildren()) do
                    if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") then
                        local humanoid = enemy:FindFirstChild("Humanoid")
                        if humanoid and humanoid.Health > 0 then
                            -- Realiza o ataque
                            game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Hit", enemy)
                            wait(attackDelay)  -- Aguardar o delay entre os ataques
                        end
                    end
                end
            end
        end
    end
end

-- Função para iniciar Auto-Click
SettingsTab:CreateButton({
    Name = "Iniciar Auto-Click",
    Callback = function()
        autoClicking = true
        spawn(startAutoClick)
    end
})

-- Função para parar Auto-Click
SettingsTab:CreateButton({
    Name = "Parar Auto-Click",
    Callback = function()
        autoClicking = false
    end
})

-- Função de Teleporte
local function teleportTo(position)
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(position)
end

-- Função para detectar e ativar Sea Events
local function checkSeaEvents()
    while true do
        wait(5)  -- Verifica a cada 5 segundos

        -- Detecta Sea Event
        for _, obj in pairs(workspace:GetChildren()) do
            if obj:IsA("Model") and obj:FindFirstChild("SeaEvent") then
                -- Evento marinho encontrado, agora teleporta o jogador para o evento
                local eventPosition = obj.PrimaryPart.Position
                teleportTo(eventPosition)

                -- Inicia a luta ou outra ação necessária
                if obj:FindFirstChild("Enemies") then
                    for _, enemy in pairs(obj.Enemies:GetChildren()) do
                        if enemy:FindFirstChild("Humanoid") then
                            -- Ataque automático no inimigo
                            game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Hit", enemy)
                        end
                    end
                end

                -- Caso o evento tenha um objetivo específico, como coletar baús ou objetos
                if obj:FindFirstChild("Objective") then
                    -- Coleta o objetivo ou item
                    game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Collect", obj.Objective)
                end
            end
        end
    end
end

-- Função para iniciar Auto-Raid
local function startRaid(raidName)
    local raid = workspace:FindFirstChild(raidName)
    if raid then
        -- Teleporta para o raid
        teleportTo(raid.PrimaryPart.Position)
        
        -- Simula ataques para completar o raid
        while true do
            wait(2)
            if raid:FindFirstChild("Enemies") then
                for _, enemy in pairs(raid.Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                        -- Atacar inimigo
                        game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Hit", enemy)
                    end
                end
            end
        end
    end
end

-- Função de Auto-Awakening
local function autoAwaken()
    -- Supondo que o jogador precise ir até a NPC de Awakening
    local awakeningNPC = workspace:FindFirstChild("AwakeningNPC")
    if awakeningNPC then
        teleportTo(awakeningNPC.Position)

        -- Espera o NPC e realiza o processo
        wait(2)
        game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Awaken", "Fruta")  -- Substitua com a fruta que deseja despertar
    end
end

-- Função de Auto-Bounty
local function autoBounty()
    while true do
        wait(5)
        for _, player in pairs(game.Players:GetChildren()) do
            if player ~= game.Players.LocalPlayer then
                local character = player.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    -- Teleporta para o jogador e ataca
                    teleportTo(character.HumanoidRootPart.Position)
                    game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Hit", character)
                end
            end
        end
    end
end

-- Função de Auto-Collect (Frutas e Baús)
local function autoCollect()
    while true do
        wait(2)
        for _, obj in pairs(workspace:GetChildren()) do
            if obj:IsA("Model") and (obj.Name == "Fruit" or obj.Name == "Chest") then
                local item = obj:FindFirstChild("HumanoidRootPart")
                if item then
                    teleportTo(item.Position)
                    -- Coleta a fruta ou baú
                    game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Collect", obj)
                end
            end
        end
    end
end

-- Adicionando os botões na interface
SettingsTab:CreateButton({
    Name = "Iniciar Raid",
    Callback = function()
        startRaid("NomeDoRaid")  -- Substitua com o nome do Raid
    end
})

SettingsTab:CreateButton({
    Name = "Auto-Awakening",
    Callback = function()
        autoAwaken()
    end
})

SettingsTab:CreateButton({
    Name = "Iniciar Auto-Bounty",
    Callback = function()
        spawn(autoBounty)
    end
})

SettingsTab:CreateButton({
    Name = "Iniciar Auto-Collect",
    Callback = function()
        spawn(autoCollect)
    end
})

SettingsTab:CreateButton({
    Name = "Ativar Sea Events",
    Callback = function()
        spawn(checkSeaEvents)
    end
