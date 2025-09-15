local fetcher = ...

local Players = game:GetService("Players")
local VirtualInputManager = game:GetService("VirtualInputManager")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local Settings = {
  RegionX = 800, RegionY = 400, RegionWidth = 400, RegionHeight = 100,
  SlapX = 960, SlapY = 540,
  GreenRGB = {0, 255, 0}, BlackRGB = {0, 0, 0}, Tolerance = 20
}

local function colorMatches(p, t, tol) for i = 1, 3 do if math.abs(p[i] - t[i]) > tol then return false end end return true end
local function getPixelColor(x, y) return getpixels and {getpixels(x, y, 1, 1)[1], getpixels(x, y, 1, 1)[2], getpixels(x, y, 1, 1)[3]} or {0, 0, 0} end
local function scan() local b, g = false, false for x = Settings.RegionX-Settings.RegionWidth/2, Settings.RegionX+Settings.RegionWidth/2 do for y = Settings.RegionY-Settings.RegionHeight/2, Settings.RegionY+Settings.RegionHeight/2 do local p = getPixelColor(x, y) if colorMatches(p, Settings.BlackRGB, Settings.Tolerance) then b = true end if colorMatches(p, Settings.GreenRGB, Settings.Tolerance) then g = true end end end return b and g end
local function slap() VirtualInputManager:SendMouseButtonEvent(Settings.SlapX, Settings.SlapY, 0, true, game, 1) wait(0.01) VirtualInputManager:SendMouseButtonEvent(Settings.SlapX, Settings.SlapY, 0, false, game, 1) print("SLAP!") end

-- Load Rayfield UI
loadstring(fetcher.get("{Repository}source"))()(function()
  local Window = Rayfield:CreateWindow({
    Name = "Slaps Hub",
    LoadingTitle = "Slaps Loading...",
    LoadingSubtitle = "by Grok",
    ConfigurationSaving = {Enabled = false},
    KeySystem = false
  })

  local Tab = Window:CreateTab("Slaps", nil)

  Tab:CreateToggle({
    Name = "Auto Slap",
    CurrentValue = false,
    Flag = "SlapsToggle",
    Callback = function(Value)
      getgenv().SlapsEnabled = Value
      print("Slaps: " .. (Value and "ON" or "OFF"))
    end,
  })
end)

RunService.Heartbeat:Connect(function() if playerGui:FindFirstChild("FishFightGui") and getgenv().SlapsEnabled and scan() then slap() end end)

spawn(function() wait(300) print("Slaps stopped.") end)
