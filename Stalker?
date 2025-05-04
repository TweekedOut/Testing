--[[
	WARNING: Heads up! This script has not been verified by ScriptBlox. Use at your own risk!
]]
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-- Animation IDs
local defaultMoveAnimId = "rbxassetid://108287960442206"
local altMoveAnimId = "rbxassetid://109671225388655"
local idleAnimId = "rbxassetid://135419935358802"

-- GUI Setup (one-time)
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MoveAnimToggleGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 140, 0, 40)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "Using Default Move"
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Parent = screenGui

-- Variables
local moveTrack, idleTrack, heartbeatConnection, toggleConnection
local useAltAnim = false

-- Helper to clean up old animation state
local function stopOldTracks()
    if idleTrack then idleTrack:Stop() idleTrack:Destroy() end
    if moveTrack then moveTrack:Stop() moveTrack:Destroy() end
    if heartbeatConnection then heartbeatConnection:Disconnect() end
    if toggleConnection then toggleConnection:Disconnect() end
end

-- Animation setup
local function setupAnimations(character)
    stopOldTracks()

    local humanoid = character:WaitForChild("Humanoid")

    -- Disable default Animate script
    local animate = character:FindFirstChild("Animate")
    if animate then animate.Disabled = true end

    -- Idle animation
    local idleAnim = Instance.new("Animation")
    idleAnim.AnimationId = idleAnimId
    idleTrack = humanoid:LoadAnimation(idleAnim)
    idleTrack.Priority = Enum.AnimationPriority.Action
    idleTrack:Play()

    -- Function to load move animation based on current toggle
    local function loadMoveTrack()
        if moveTrack then moveTrack:Stop() moveTrack:Destroy() end
        local moveAnim = Instance.new("Animation")
        moveAnim.AnimationId = useAltAnim and altMoveAnimId or defaultMoveAnimId
        moveTrack = humanoid:LoadAnimation(moveAnim)
        moveTrack.Priority = Enum.AnimationPriority.Movement
    end

    loadMoveTrack()

    -- Movement logic loop
    heartbeatConnection = RunService.Heartbeat:Connect(function()
        local speed = humanoid.MoveDirection.Magnitude

        -- Clean default idle animations
        for _, track in pairs(humanoid:GetPlayingAnimationTracks()) do
            if track.Animation.AnimationId ~= idleAnimId
                and track.Animation.AnimationId ~= moveTrack.Animation.AnimationId then
                if track.Priority == Enum.AnimationPriority.Core
                    or track.Name == "idle"
                    or track.Animation.AnimationId:lower():find("idle") then
                    track:Stop()
                end
            end
        end

        if speed > 0.01 then
            if not moveTrack.IsPlaying then
                idleTrack:Stop()
                moveTrack:Play()
            end
        else
            if not idleTrack.IsPlaying then
                moveTrack:Stop()
                idleTrack:Play()
            end
        end
    end)

    -- GUI toggle logic
    toggleConnection = toggleButton.MouseButton1Click:Connect(function()
        useAltAnim = not useAltAnim
        toggleButton.Text = useAltAnim and "Using Alt Move" or "Using Default Move"
        loadMoveTrack()
    end)
end

-- Setup on current and future character loads
if player.Character then
    setupAnimations(player.Character)
end

player.CharacterAdded:Connect(function(char)
    setupAnimations(char)
end)
