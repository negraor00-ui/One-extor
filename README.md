-- Configurações — Mago do Oeste
local Config = {}

-- ⚙️ CAMPO DE PRECISÃO (FOV) — AJUSTE AQUI!
Config.FOV = 90          -- Tamanho do campo em graus (30 = pequeno, 90 = médio, 120 = amplo)
Config.ForcaMira = 0.7   -- Força da ajuda: 0 = sem ajuda, 1 = trava total
Config.AlcanceMax = 100  -- Distância máxima de detecção

-- Visão de Energia (ESP)
Config.ModoVisao = {
    Ativo = true,
    CorInimigo = Color3.fromRGB(255, 60, 60),
    CorAliado = Color3.fromRGB(60, 255, 120),
    Espessura = 0.1
}

return Config
-- VALIDAÇÃO NO SERVIDOR — NÃO DEIXA NINGUÉM TRApacear
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Config = require(ReplicatedStorage.ConfigCombate)

local EventoMira = Instance.new("RemoteEvent")
EventoMira.Name = "EventoMira"
EventoMira.Parent = ReplicatedStorage

-- Verifica se o alvo é válido e está ao alcance
EventoMira.OnServerEvent:Connect(function(jogador, alvo, distancia)
    if not alvo or not alvo:IsA("BasePart") then return end
    if not alvo.Parent:FindFirstChild("Humanoid") then return end
    
    local personagem = jogador.Character
    if not personagem then return end
    
    local posicaoJogador = personagem.HumanoidRootPart.Position
    local posicaoAlvo = alvo.Position
    local dist = (posicaoJogador - posicaoAlvo).Magnitude
    
    -- Trava de segurança
    if dist > Config.AlcanceMax then return end
    
    -- Tudo certo = confirma para o cliente
    return true
end)

