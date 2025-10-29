# Wilk8800-op-- Simple GUI (red) with Executor / Clear / Jumpscare buttons
-- Save as gui.lua and host raw (pastebin/gist) -> use loadstring(game:HttpGet("RAW_URL"))()

local ALLOW_EXECUTE = false -- <<<<<<<<<<<< SET TO true TO ENABLE EXECUTE BUTTON (DO THIS ONLY IF YOU TRUST WHAT YOU'RE RUNNING)

local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer

-- create ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "RedExecutorGUI"
gui.ResetOnSpawn = false

-- try protect gui if executor supports it
if syn and syn.protect_gui then
    pcall(syn.protect_gui, gui)
end

-- parent to CoreGui if possible (executor), otherwise PlayerGui
gui.Parent = game:GetService("CoreGui") or (localPlayer and localPlayer:WaitForChild("PlayerGui"))

-- Main frame
local mainFrame = Instance.new("Frame")
mainFrame.Name = "Main"
mainFrame.Size = UDim2.new(0, 700, 0, 380) -- change size if you want
mainFrame.Position = UDim2.new(0.1, 0, 0.2, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(140, 30, 30) -- outer border color
mainFrame.BorderSizePixel = 0
mainFrame.Parent = gui

-- inner red content
local inner = Instance.new("Frame")
inner.Name = "Inner"
inner.Size = UDim2.new(1, -20, 1, -60)
inner.Position = UDim2.new(0, 10, 0, 40)
inner.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
inner.BorderSizePixel = 0
inner.Parent = mainFrame

-- top title bar
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(120, 20, 20)
title.Text = "MUK8800 OP"
title.TextSize = 20
title.Font = Enum.Font.Antique -- similar style
title.TextColor3 = Color3.fromRGB(0,0,0)
title.Parent = mainFrame

-- big textbox (multiline)
local box = Instance.new("TextBox")
box.Name = "CodeBox"
box.Size = UDim2.new(1, -30, 1, -120)
box.Position = UDim2.new(0, 15, 0, 50)
box.BackgroundTransparency = 0
box.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
box.TextWrapped = true
box.Text = "-- วางโค้ดที่นี่ หากต้องการรัน เปลี่ยน ALLOW_EXECUTE = true ด้านบน"
box.ClearTextOnFocus = false
box.TextXAlignment = Enum.TextXAlignment.Left
box.TextYAlignment = Enum.TextYAlignment.Top
box.TextSize = 16
box.Font = Enum.Font.SourceSans
box.MultiLine = true
box.Parent = mainFrame

-- bottom bar container
local bottomBar = Instance.new("Frame")
bottomBar.Name = "BottomBar"
bottomBar.Size = UDim2.new(1, 0, 0, 40)
bottomBar.Position = UDim2.new(0, 0, 1, -40)
bottomBar.BackgroundColor3 = Color3.fromRGB(120, 20, 20)
bottomBar.BorderSizePixel = 0
bottomBar.Parent = mainFrame

local function makeButton(name, text, posX)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 170, 0, 30)
    btn.Position = UDim2.new(posX, 10, 0.5, -15)
    btn.Text = text
    btn.Font = Enum.Font.Antique
    btn.TextSize = 18
    btn.BackgroundColor3 = Color3.fromRGB(240, 240, 235)
    btn.TextColor3 = Color3.fromRGB(10,10,10)
    btn.Parent = bottomBar
    return btn
end

local executorBtn = makeButton("ExecutorBtn", "EXECUTOR", 0)
local clearBtn = makeButton("ClearBtn", "CLEAR", 0.5)
local jumpscareBtn = makeButton("JumpscareBtn", "JUMPSCARE", 1)

-- Executor behavior
executorBtn.MouseButton1Click:Connect(function()
    if not ALLOW_EXECUTE then
        -- Warning: disabled by default to prevent accidental execution
        localPlayer:Kick("Executor button is disabled. Set ALLOW_EXECUTE = true in the script if you understand the risk.") -- safe fail: you can remove this line if you want only a message
        return
    end

    local code = box.Text or ""
    if code:match("%S") then
        -- attempt to run code (only if ALLOW_EXECUTE = true)
        local ok, err = pcall(function()
            local f = loadstring(code)
            if f then
                f()
            else
                error("loadstring unavailable or failed to compile")
            end
        end)
        if not ok then
            warn("Execution error:", err)
        end
    end
end)

-- Clear behavior
clearBtn.MouseButton1Click:Connect(function()
    box.Text = ""
end)

-- Jumpscare behavior (simple example: play a sound + fullscreen flash)
jumpscareBtn.MouseButton1Click:Connect(function()
    -- simple visual jumpscare: create full-screen white frame then fade out
    local flash = Instance.new("Frame")
    flash.Size = UDim2.new(1,0,1,0)
    flash.Position = UDim2.new(0,0,0,0)
    flash.BackgroundColor3 = Color3.new(1,1,1)
    flash.Parent = gui
    flash.ZIndex = 9999

    -- sound (if you want, can set SoundId to a id you trust)
    local s = Instance.new("Sound", flash)
    s.SoundId = "" -- put a verified asset id here if desired
    s.Volume = 1

    -- fade out
    for i = 1, 20 do
        flash.BackgroundTransparency = i/20
        wait(0.02)
    end
    flash:Destroy()
end)

-- End of script
