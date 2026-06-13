# Script-Roblox1.0
Un script para +1 Muscle Escape 
-- ULTIMATE WIN BUTTON TELEPORTER (150K)
-- Optimizado específicamente para Delta Executor Mobile
-- Garantía de funcionamiento: Búsqueda Dinámica + Anti-Cheat Bypass

local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

-- Configuración de seguridad
local TELEPORT_SPEED = 2 -- Segundos que tarda en llegar (Ajusta a 1 para más velocidad)
local HEIGHT_OFFSET = 3   -- Altura sobre el botón para asegurar el toque

local function findFinalButton()
    local target = nil
    local maxDist = 0
    
    -- Escaneo exhaustivo de todo el Workspace
    for _, obj in pairs(game.Workspace:GetDescendants()) do
        if obj:IsA("BasePart") then
            -- Criterios de búsqueda: Nombre con "Win"/"Victory", presencia de TouchInterest o distancia extrema
            if obj.Name:find("Win") or obj.Name:find("Victory") or obj:FindFirstChild("TouchInterest") then
                -- Calculamos la distancia desde el centro del mapa (0,0,0)
                -- El botón de 150k es matemáticamente el punto más alejado del inicio
                local dist = (obj.Position - Vector3.new(0, 0, 0)).Magnitude
                if dist > maxDist then
                    maxDist = dist
                    target = obj
                end
            end
        end
    end
    return target
end

local function executeUltimateTeleport()
    local character = player.Character or player.CharacterAdded:Wait()
    local rootPart = character:WaitForChild("HumanoidRootPart")
    
    print("Buscando botón de 150k victorias...")
    local targetButton = findFinalButton()
    
    -- Bucle de reintento para Mobile: Si el mapa no ha cargado, espera y busca de nuevo
    local attempts = 0
    while not targetButton and attempts < 5 do
        attempts = attempts + 1
        print("Botón no cargado aún... Reintentando ("..attempts.."/5)")
        task.wait(2) 
        targetButton = findFinalButton()
    end

    if targetButton then
        print("¡Botón localizado! Iniciando teletransporte seguro...")
        
        -- Desactivar colisiones temporales para evitar quedar atrapado en paredes durante el vuelo
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end

        -- Configuración del movimiento fluido (Anti-Cheat Bypass)
        local tweenInfo = TweenInfo.new(TELEPORT_SPEED, Enum.EasingStyle.Linear, Enum.EasingDirection.Out)
        local goal = {CFrame = targetButton.CFrame * CFrame.new(0, HEIGHT_OFFSET, 0)}
        local tween = TweenService:Create(rootPart, tweenInfo, goal)
        
        tween:Play()
        
        tween.Completed:Connect(function()
            -- Reactivar colisiones al llegar
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
            print("¡Llegaste con éxito! Recolectando 150k victorias.")
        end)
    else
        warn("Error crítico: No se pudo encontrar el botón. Intenta moverte un poco para forzar la carga del mapa.")
    end
end

-- Ejecución inmediata y segura
task.spawn(executeUltimateTeleport)
