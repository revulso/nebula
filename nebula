-- // shockwinning on Discord // @SHOCKWAVlN

getgenv().Nebula = {
    Toggled = true,
    DamageAmplifier = true, -- // Doesn't use FTI/firetouchinterest

    Size = {
        X = 3.5,
        Y = 3.5,
        Z = 3.5,

        Massless = true,

        Increment = 0.5,
        Decrement = 0.5
    },

    Visualizer = {
        Enabled = false,
        Shape = "Ball", -- // Sphere/Ball, Box/Block, Cylinder
        Colour = Color3.fromRGB(0, 0, 0),
        OutlineColour = Color3.fromRGB(118, 51, 232)
    },

    ESP = {
        Enabled = true,
    },

    Keybinds = {
        Toggle = Enum.KeyCode["R"],
        Increment = Enum.KeyCode["Q"],
        Decrement = Enum.KeyCode["E"],
        Visualizer = Enum.KeyCode["T"],
        DamageAmplifier = Enum.KeyCode["G"],
        CFrameSpeed = Enum.KeyCode["C"],
        Freeze = Enum.KeyCode["V"],
        ToggleUI = Enum.KeyCode["RightBracket"],
        ToggleESP = Enum.KeyCode["B"],
        Notifications = Enum.KeyCode["N"],
        Autoclick = Enum.KeyCode["F"]
    },

    Checks = {
        DetectionDistance = 20,
        InvisibleCheck = true,
        LungeCheck = true,
        FakeHitbox = false,
        FakeHandleDeepSearch = false,

        Notifications = true,
        Autoclick = false,
        AutoclickFakeIcon = true
    }
}

----------------------------------------------------------------------------------------------------

local function DeepCopy(original)
    local copy = {}
    for k, v in pairs(original) do
        if typeof(v) == "table" then
            copy[k] = v
        end
    end
    return copy
end

local NebulaInternal = DeepCopy(Nebula)

local Services = {
    Players = game:GetService("Players"),
    RunService = game:GetService("RunService"),
    Workspace = game:GetService("Workspace"),
    ReplicatedStorage = game:GetService("ReplicatedStorage"),
    CoreGui = game:GetService("CoreGui"),
    UserInputService = game:GetService("UserInputService"),
    MarketPlaceService = game:GetService("MarketplaceService"),
    HttpService = game:GetService("HttpService")
}

local HTTP_REQUEST = (syn and syn.request) or (fluxus and fluxus.request) or (http and http.request) or http_request or request
local Libraries = {
    Notification = loadstring(HTTP_REQUEST({Url = "https://raw.githubusercontent.com/shockwinning/Nebula/main/Notifications.lua", Method = "GET"}).Body)(),
    UI = loadstring(HTTP_REQUEST({Url = "https://raw.githubusercontent.com/shockwinning/Nebula/main/UI.lua", Method = "GET"}).Body)()
}

local Vars = {
    LocalPlayer = Services.Players.LocalPlayer,
    Camera = Services.Workspace.CurrentCamera,
    Mouse = Services.Players.LocalPlayer:GetMouse(),
    Part = Instance.new("Part")
}

local BlacklistedTools = {}
local CharacterDescendants = {}
local HandleAddedConnection = nil

local Handle = nil
local FakeHandles = nil

local HalfLungeDebounce = false

local function GetFakeHandles()
    local Found = {}
    if Handle then
        if FakeHandleDeepSearch then
            for i, v in pairs(workspace:GetDescendants()) do
                if v:IsA("BasePart") and (v:FindFirstChildOfClass("TouchTransmitter") or #getconnections(v.LocalSimulationTouched) > 0) and (Handle.Position - v.Position).Magnitude <= 1 and v ~= Handle and v.Parent and not v.Parent:IsA("Accessory") then
                    table.insert(Found, v)
                end
            end
            if not table.find(FakeHandles, FakeHandle) then
                table.insert(FakeHandles, FakeHandle)
            end
            return Found
        end
        for i, v in pairs(workspace.Terrain:GetDescendants()) do
            if v:IsA("BasePart") and (v:FindFirstChildOfClass("TouchTransmitter") or #getconnections(v.LocalSimulationTouched) > 0) and (Handle.Position - v.Position).Magnitude <= 1 and v ~= Handle and v.Parent and not v.Parent:IsA("Accessory") then
                table.insert(Found, v)
            end
        end
        for i, v in pairs(workspace.CurrentCamera:GetDescendants()) do
            if v:IsA("BasePart") and (v:FindFirstChildOfClass("TouchTransmitter") or #getconnections(v.LocalSimulationTouched) > 0) and (Handle.Position - v.Position).Magnitude <= 1 and v ~= Handle and v.Parent and not v.Parent:IsA("Accessory") then
                table.insert(Found, v)
            end
        end
        for i, v in pairs(Vars.LocalPlayer.Character:GetDescendants()) do
            if v:IsA("BasePart") and (v:FindFirstChildOfClass("TouchTransmitter") or #getconnections(v.LocalSimulationTouched) > 0) and (Handle.Position - v.Position).Magnitude <= 1 and v ~= Handle and v.Parent and not v.Parent:IsA("Accessory") then
                table.insert(Found, v)
            end
        end
    end
    for i, v in pairs(Found) do
        print(v:GetFullName())
        if not table.find(FakeHandles, v) then
            table.insert(FakeHandles, v)
        end
    end
    return Found
end

local function HandleConnections(Object, Event, State)
    for _, Connection in next, getconnections(Object[Event]) do
        Connection[State](Connection)
    end
end

local function HandleGPCSConnections(Object, Property, State)
    for _, Connection in next, getconnections(Object:GetPropertyChangedSignal(Property)) do
        Connection[State](Connection)
    end
end

local function ActivateTool(tool, state)
    if state == "Lunge" then
        HandleConnections(tool, "Activated", "Disable")
        HandleConnections(Services.UserInputService, "InputBegan", "Disable")
        tool:Activate()
        tool:Activate()
        HandleConnections(Services.UserInputService, "InputBegan", "Enable")
        HandleConnections(tool, "Activated", "Enable")
    elseif state == "Half Lunge" and not HalfLungeDebounce then
        HalfLungeDebounce = true
        HandleConnections(tool, "Activated", "Disable")
        HandleConnections(Services.UserInputService, "InputBegan", "Disable")
        tool:Activate()
        HandleConnections(Services.UserInputService, "InputBegan", "Enable")
        HandleConnections(tool, "Activated", "Enable")
        task.wait(0.25)
        HalfLungeDebounce = false
    end
end

local function UpdateHandle()
    if HandleAddedConnection then
        HandleAddedConnection:Disconnect()
        HandleAddedConnection = nil
    end
    for _, Child in pairs(Vars.LocalPlayer.Character:GetChildren()) do
        if Child:IsA("Tool") and Child:FindFirstChild("Handle") then
            local _Handle = Child:FindFirstChild("Handle")
            if _Handle:FindFirstChildOfClass("TouchTransmitter") then
                Handle = _Handle
            end
        end
    end
    for _, Child in pairs(Vars.LocalPlayer.Backpack:GetChildren()) do
        if Child:IsA("Tool") and Child:FindFirstChild("Handle") then
            local _Handle = Child:FindFirstChild("Handle")
            if _Handle:FindFirstChildOfClass("TouchTransmitter") then
                Handle = _Handle
            end
        end
    end
    HandleAddedConnection = Vars.LocalPlayer.Character.ChildAdded:Connect(function(Child)
        if Child:IsA("Tool") and Child:FindFirstChild("Handle") then
            local _Handle = Child:FindFirstChild("Handle")
            if _Handle:FindFirstChildOfClass("TouchTransmitter") then
                Handle = _Handle
            end
        end
    end)
end

FakeHandles = GetFakeHandles()
UpdateHandle()

if not Handle then
    if NebulaInternal.Checks.Notifications then
        Libraries.Notification:message({
            Title = "Nebula",
            Description = "Handle not found. Obtain a sword to resume execution.",
            Icon = 6023426926
        })
    end

    repeat
        task.wait()
    until Handle
    
    if NebulaInternal.Checks.Notifications then
        Libraries.Notification:message({
            Title = "Nebula",
            Description = "Handle found. Execution resumed.",
            Icon = 6023426926
        })
    end
end

for i, v in pairs(Vars.LocalPlayer.Character:GetDescendants()) do
    if v:IsA("BasePart") then
        table.insert(CharacterDescendants, v)
    end
end

local OriginalHandleProperties = {
    Size = Handle.Size,
    ExtentsSize = Handle.ExtentsSize,
    Massless = Handle.Massless,
    Mass = Handle.Mass,
    AssemblyMass = Handle.AssemblyMass,
    CanQuery = Handle.CanQuery
}

local Visualizers, Welds = {}, {}
Visualizers[1] = Instance.new("Part")
if syn and syn.protect_gui then
    syn.protect_gui(Visualizers[1])
end
Visualizers[1].Name = string.rep("\n", math.random(1, 10))
Visualizers[1].BottomSurface = Enum.SurfaceType.Smooth
Visualizers[1].Transparency = 0
Visualizers[1].TopSurface = Enum.SurfaceType.Smooth
Visualizers[1].Color = NebulaInternal.Visualizer.OutlineColour
Visualizers[1].Massless = true
Visualizers[1].CanCollide = false
Visualizers[1].CanTouch = false
Visualizers[1].CanQuery = false
Visualizers[1].Material = Enum.Material.ForceField
Visualizers[1].Size = Vector3.new(1, 1, 1)
Visualizers[1].CastShadow = false
Visualizers[1].Shape = Enum.PartType[NebulaInternal.Visualizer.Shape]
Visualizers[1].Parent = nil

Visualizers[2] = Instance.new("Part")
if syn and syn.protect_gui then
    syn.protect_gui(Visualizers[2])
end
Visualizers[2].Name = string.rep("\n", math.random(1, 10))
Visualizers[2].BottomSurface = Enum.SurfaceType.Smooth
Visualizers[2].Transparency = 0.4
Visualizers[2].TopSurface = Enum.SurfaceType.Smooth
Visualizers[2].Color = NebulaInternal.Visualizer.Colour
Visualizers[2].Massless = true
Visualizers[2].CanCollide = false
Visualizers[2].CanTouch = false
Visualizers[2].CanQuery = false
Visualizers[2].Material = Enum.Material.Neon
Visualizers[2].Size = Vector3.new(1, 1, 1)
Visualizers[2].CastShadow = false
Visualizers[2].Shape = Enum.PartType[NebulaInternal.Visualizer.Shape]
Visualizers[2].Parent = nil

Welds[1] = Instance.new("WeldConstraint")
if syn and syn.protect_gui then
    syn.protect_gui(Welds[1])
end
Welds[1].Parent = Visualizers[1]

Welds[2] = Instance.new("WeldConstraint")
if syn and syn.protect_gui then
    syn.protect_gui(Welds[2])
end
Welds[2].Parent = Visualizers[2]

local FakeHitbox = {}
pcall(function()
    FakeHitbox.Hitbox = Handle:Clone()
    if syn and syn.protect_gui then
        syn.protect_gui(FakeHitbox.Hitbox)
    end
    FakeHitbox.Hitbox:ClearAllChildren()
    FakeHitbox.Hitbox.Transparency = 1
    FakeHitbox.Hitbox.Size = OriginalHandleProperties.Size
    FakeHitbox.Hitbox.Massless = true
    FakeHitbox.Hitbox.CanCollide = false
    FakeHitbox.Hitbox.CanTouch = false
    FakeHitbox.Hitbox.CanQuery = false
    FakeHitbox.Weld = Instance.new("WeldConstraint")
    if syn and syn.protect_gui then
        syn.protect_gui(FakeHitbox.Hitbox)
    end
    FakeHitbox.Weld.Parent = FakeHitbox.Hitbox
    FakeHitbox.Weld.Part0 = FakeHitbox.Hitbox
end)

local WindowFocused = (iswindowactive and iswindowactive()) or (isrbxactive and isrbxactive()) or true
Services.UserInputService.WindowFocused:Connect(function()
    WindowFocused = true
end)
Services.UserInputService.WindowFocusReleased:Connect(function()
    WindowFocused = false
end)

getgenv().PlayerDrawings = {}
getgenv().Utility = {}
getgenv().esp_stopped = false
Utility.Settings = {
    Line = {
        Thickness = 1,
        Color = Color3.fromRGB(0, 255, 0)
    },
    Text = {
        Size = 13,
        Center = true,
        Outline = true,
        Font = Drawing.Fonts.Plex,
        Color = Color3.fromRGB(255, 255, 255)
    },
    Square = {
        Thickness = 1,
        Color = Color3.fromRGB(255, 255, 255),
        Filled = false,
    },
    Triangle = {
        Color = Color3.fromRGB(255, 255, 255),
        Filled = true,
        Visible = false,
        Thickness = 1,
    }
}

----------------------------------------------------------------------------------------------------

-- fuck Adonis hook checks
for i, v in next, getgc(true) do
    if typeof(v) == "table" and rawget(v, "namecallInstance") then
        table.clear(v)
    end
end

local ClonedFunctions = {
    Clone = clonefunction ~= nil and clonefunction((Handle or Vars.Part).Clone) or (Handle or Vars.Part).Clone,
    GetConnectedParts = clonefunction ~= nil and clonefunction((Handle or Vars.Part).GetConnectedParts) or (Handle or Vars.Part).GetConnectedParts
}
local Hooks = {}

Hooks.GetMass = hookfunction(Handle.GetMass, newcclosure(function(...)
    if not checkcaller() then
        return OriginalHandleProperties.Mass
    end
    return Hooks.GetMass(...)
end))

Hooks.Clone = hookfunction(Handle.Clone, newcclosure(function(...)
    if not checkcaller() then
        return ClonedFunctions.Clone(Handle)
    end
    return Hooks.Clone(...)
end))

Hooks.GetConnectedParts = hookfunction(Handle.GetConnectedParts, newcclosure(function(...)
    if not checkcaller() then
        local FakeConnectedParts = ClonedFunctions.GetConnectedParts(Handle)
        if FakeConnectedParts and typeof(FakeConnectedParts) == "table" then
            if table.find(FakeConnectedParts, Visualizers[1]) then
                table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Visualizers[1]))
            end
            if table.find(FakeConnectedParts, Visualizers[2]) then
                table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Visualizers[2]))
            end
            if table.find(FakeConnectedParts, Welds[1]) then
                table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Welds[1]))
            end
            if table.find(FakeConnectedParts, Welds[2]) then
                table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Welds[2]))
            end
            for i, v in pairs(FakeHitbox) do
                if table.find(FakeConnectedParts, v) then
                    table.remove(FakeConnectedParts, table.find(FakeConnectedParts, v))
                end
            end
        end
        return FakeConnectedParts
    end
    return Hooks.GetConnectedParts(...)
end))

Hooks.Index = nil; Hooks.Index = hookmetamethod(game, "__index", newcclosure(function(self, property)
    if not checkcaller() then
        if property and typeof(property) == "string" then -- // ikik arg-guard but some executors don't have it
            local FilteredProperty = property:split("\0")[1]

            if self == Handle or (FakeHandles[self] or table.find(FakeHandles, self)) then
                if FilteredProperty == "Size" or FilteredProperty == "size" then
                    return OriginalHandleProperties.Size
                elseif FilteredProperty == "ExtentsSize" or FilteredProperty == "extentsSize" then
                    return OriginalHandleProperties.ExtentsSize
                elseif FilteredProperty == "Mass" or FilteredProperty == "mass" then
                    return OriginalHandleProperties.Mass
                elseif FilteredProperty == "Massless" or FilteredProperty == "massless" then
                    return OriginalHandleProperties.Massless
                elseif FilteredProperty == "AssemblyMass" or FilteredProperty == "assemblyMass" then
                    return OriginalHandleProperties.AssemblyMass
                elseif FilteredProperty == "CanQuery" or FilteredProperty == "canQuery" then
                    return OriginalHandleProperties.CanQuery
                end
            elseif CharacterDescendants[self] or table.find(CharacterDescendants, self) then
                if FilteredProperty == "AssemblyMass" or FilteredProperty == "assemblyMass" then
                    return OriginalHandleProperties.AssemblyMass
                end
            end
        end
    end
    return Hooks.Index(self, property)
end))

Hooks.Namecall = nil; Hooks.Namecall = hookmetamethod(game, "__namecall", newcclosure(function(self, ...)
    if not checkcaller() then
        local Args = {...}
        local Method = getnamecallmethod()
        if self == Handle or (FakeHandles[self] or table.find(FakeHandles, self)) then
            if Method == "GetMass" or Method == "getMass" then
                return OriginalHandleProperties.Mass
            elseif Method == "Clone" or Method == "clone" then
                local FakeHandle = ClonedFunctions.Clone(Handle)
                return FakeHandle 
            elseif Method == "GetConnectedParts" or Method == "getConnectedParts" then
                local FakeConnectedParts = ClonedFunctions.GetConnectedParts(Handle)
                if FakeConnectedParts and typeof(FakeConnectedParts) == "table" then
                    if table.find(FakeConnectedParts, Visualizers[1]) then
                        table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Visualizers[1]))
                    end
                    if table.find(FakeConnectedParts, Visualizers[2]) then
                        table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Visualizers[2]))
                    end
                    if table.find(FakeConnectedParts, Welds[1]) then
                        table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Welds[1]))
                    end
                    if table.find(FakeConnectedParts, Welds[2]) then
                        table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Welds[2]))
                    end
                    for i, v in pairs(FakeHitbox) do
                        if table.find(FakeConnectedParts, v) then
                            table.remove(FakeConnectedParts, table.find(FakeConnectedParts, v))
                        end
                    end
                end
                return FakeConnectedParts
            end
        end
    end
    return Hooks.Namecall(self, ...)
end))

Hooks.NewIndex = nil; Hooks.NewIndex = hookmetamethod(game, "__newindex", newcclosure(function(t, i, v)
    if not checkcaller() then
        local FilteredProperty = tostring(i):split("\0")[1]
        if t == Handle or (FakeHandles[t] or table.find(FakeHandles, t)) then
            if FilteredProperty == "Size" or FilteredProperty == "size" then
                if typeof(v) == "Vector3" then
                    OriginalHandleProperties.Size = v
                    return
                end
            elseif FilteredProperty == "ExtentsSize" or FilteredProperty == "extentsSize" then
                if typeof(v) == "Vector3" then
                    OriginalHandleProperties.ExtentsSize = v
                    return
                end
            elseif FilteredProperty == "CanQuery" or FilteredProperty == "canQuery" then
                if typeof(v) == "boolean" then
                    OriginalHandleProperties.CanQuery = v
                    return
                end
            elseif FilteredProperty == "Massless" or FilteredProperty == "massless" then
                if typeof(v) == "boolean" then
                    OriginalHandleProperties.Massless = v
                    return
                end
            end
        end
    end
    return Hooks.NewIndex(t, i, v)
end))

Vars.LocalPlayer.CharacterAdded:Connect(function(Character)
    UpdateHandle()
    FakeHandles = GetFakeHandles()

    repeat
        task.wait()
    until Character:FindFirstChild("Head")
    
    table.clear(CharacterDescendants)
    for i, v in pairs(Character:GetDescendants()) do
        if v:IsA("BasePart") then
            table.insert(CharacterDescendants, v)
        end
    end

    Hooks.GetMass = hookfunction(Handle.GetMass, newcclosure(function(...)
        if not checkcaller() then
            return OriginalHandleProperties.Mass
        end
        return Hooks.GetMass(...)
    end))
    
    Hooks.Clone = hookfunction(Handle.Clone, newcclosure(function(...)
        if not checkcaller() then
            return ClonedFunctions.Clone(Handle)
        end
        return Hooks.Clone(...)
    end))
    
    Hooks.GetConnectedParts = hookfunction(Handle.GetConnectedParts, newcclosure(function(...)
        if not checkcaller() then
            local FakeConnectedParts = ClonedFunctions.GetConnectedParts(Handle)
            if FakeConnectedParts and typeof(FakeConnectedParts) == "table" then
                if table.find(FakeConnectedParts, Visualizers[1]) then
                    table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Visualizers[1]))
                end
                if table.find(FakeConnectedParts, Visualizers[2]) then
                    table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Visualizers[2]))
                end
                if table.find(FakeConnectedParts, Welds[1]) then
                    table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Welds[1]))
                end
                if table.find(FakeConnectedParts, Welds[2]) then
                    table.remove(FakeConnectedParts, table.find(FakeConnectedParts, Welds[2]))
                end
                for i, v in pairs(FakeHitbox) do
                    if table.find(FakeConnectedParts, v) then
                        table.remove(FakeConnectedParts, table.find(FakeConnectedParts, v))
                    end
                end
            end
            return FakeConnectedParts
        end
        return Hooks.GetConnectedParts(...)
    end))
end)

----------------------------------------------------------------------------------------------------

local Status = "User"
if Vars.LocalPlayer.UserId == 115937702 then
    Status = "Developer"
end

for i, v in pairs(Services.CoreGui:GetChildren()) do
	if v:IsA("ScreenGui") and v.Name:find("-") then
		v:Destroy()
	end
end

local Window = Libraries.UI.new("Nebula", "Nebula", Status)
local MainCategory = Window:Category("Main", "http://www.roblox.com/asset/?id=8395621517")
local MainSubCategory = MainCategory:Button("Main", "http://www.roblox.com/asset/?id=8395621517")

local SwordSection = MainSubCategory:Section("Sword Modifications", "Left")
local VisualizerSection = MainSubCategory:Section("Visualizer", "Right")
local KeybindsSection = MainSubCategory:Section("Keybinds", "Left")
local ChecksSection = MainSubCategory:Section("Checks", "Right")
local ESPSection = MainSubCategory:Section("ESP", "Right")

SwordSection:Toggle({
    Title = "Enabled",
    Description = "Allows sword to be resizable",
    Default = Nebula.Toggled
    }, function(value)
        NebulaInternal.Toggled = value
    end
)

SwordSection:Toggle({
    Title = "Damage Amplifier",
    Description = "Amplifes your damage dealt",
    Default = Nebula.Keybinds.DamageAmplifier
    }, function(value)
        NebulaInternal.Keybinds.DamageAmplifier = value
    end
)

SwordSection:Toggle({
    Title = "Fake Handle Deep Search",
    Description = "Searches entire workspace for fake handles. Lags in big games.",
    Default = Nebula.Checks.FakeHandleDeepSearch
    }, function(value)
        NebulaInternal.Checks.FakeHandleDeepSearch = value
    end
)

SwordSection:Textbox({
    Title = "Size X",
    Description = "X size of the sword",
    Default = tostring(Nebula.Size.X)
    }, function(value)
        NebulaInternal.Size.X = tonumber(value)
    end
)

SwordSection:Textbox({
    Title = "Size Y",
    Description = "Y size of the sword",
    Default = tostring(Nebula.Size.X)
    }, function(value)
        NebulaInternal.Size.Y = tonumber(value)
    end
)

SwordSection:Textbox({
    Title = "Size Z",
    Description = "Z size of the sword",
    Default = tostring(Nebula.Size.Z)
    }, function(value)
        NebulaInternal.Size.Z = tonumber(value)
    end
)

SwordSection:Toggle({
    Title = "Massless",
    Description = "Removes sword weight",
    Default = Nebula.Size.Massless
    }, function(value)
        NebulaInternal.Size.Massless = value
    end
)

VisualizerSection:Toggle({
    Title = "Enabled",
    Description = "Allows you to view the reach hitbox",
    Default = Nebula.Visualizer.Enabled
    }, function(value)
        NebulaInternal.Visualizer.Enabled = value
    end
)

VisualizerSection:Textbox({
    Title = "Shape",
    Description = "Shape of the visualizer",
    Default = Nebula.Visualizer.Shape
    }, function(value)
        NebulaInternal.Visualizer.Shape = value
    end
)

VisualizerSection:ColorPicker({
    Title = "Colour",
    Description = "The colour of the visualizer",
    Default = Nebula.Visualizer.Colour
    }, function(value)
        NebulaInternal.Visualizer.Colour = value
    end
)

VisualizerSection:ColorPicker({
    Title = "Outline Colour",
    Description = "The colour of the outline of the visualizer",
    Default = Nebula.Visualizer.OutlineColour
    }, function(value)
        NebulaInternal.Visualizer.OutlineColour = value
    end
)

KeybindsSection:Keybind({
    Title = "Increment Size",
    Description = "Keybind to increase size",
    Default = Nebula.Keybinds.Increment,
    }, function()
        NebulaInternal.Size.X = NebulaInternal.Size.X + NebulaInternal.Size.Increment
        NebulaInternal.Size.Y = NebulaInternal.Size.Y + NebulaInternal.Size.Increment
        NebulaInternal.Size.Z = NebulaInternal.Size.Z + NebulaInternal.Size.Increment
        if NebulaInternal.Checks.Notifications then
            Libraries.Notification:message({
                Title = "Nebula",
                Description = "Size incremented to <b><font color='rgb(" .. tostring(math.clamp(math.ceil(NebulaInternal.Visualizer.OutlineColour.R * 255), 0, 255)) .. ", " .. tostring(math.clamp(math.ceil(NebulaInternal.Visualizer.OutlineColour.G * 255), 0, 255)) .. ", " .. tostring(math.clamp(math.ceil(NebulaInternal.Visualizer.OutlineColour.B * 255), 0, 255)) .. ")'>" .. tostring(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) .. "</font></b>",
                Icon = 6023426926
            })
        end
    end
)

KeybindsSection:Keybind({
    Title = "Decrement Size",
    Description = "Keybind to increase size",
    Default = Nebula.Keybinds.Decrement,
    }, function()
        NebulaInternal.Size.X = NebulaInternal.Size.X - NebulaInternal.Size.Decrement
        NebulaInternal.Size.Y = NebulaInternal.Size.Y - NebulaInternal.Size.Decrement
        NebulaInternal.Size.Z = NebulaInternal.Size.Z - NebulaInternal.Size.Decrement

        if NebulaInternal.Checks.Notifications then
            Libraries.Notification:message({
                Title = "Nebula",
                Description = "Size decremented to <b><font color='rgb(" .. tostring(math.clamp(math.ceil(NebulaInternal.Visualizer.OutlineColour.R * 255), 0, 255)) .. ", " .. tostring(math.clamp(math.ceil(NebulaInternal.Visualizer.OutlineColour.G * 255), 0, 255)) .. ", " .. tostring(math.clamp(math.ceil(NebulaInternal.Visualizer.OutlineColour.B * 255), 0, 255)) .. ")'>" .. tostring(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) .. "</font></b>",
                Icon = 6023426926
            })
        end
    end
)

KeybindsSection:Keybind({
    Title = "Visualizer",
    Description = "Keybind for whether the visualizer is visible or not",
    Default = Nebula.Keybinds.Visualizer,
    }, function()
        NebulaInternal.Visualizer.Enabled = not NebulaInternal.Visualizer.Enabled
    end
)

KeybindsSection:Keybind({
    Title = "Damage Amplifier",
    Description = "Keybind for whether the visualizer is visible or not",
    Default = Nebula.Keybinds.DamageAmplifier,
    }, function()
        NebulaInternal.Visualizer.Enabled = not NebulaInternal.Visualizer.Enabled
    end
)

KeybindsSection:Keybind({
    Title = "Toggle UI",
    Description = "Keybind to toggle the UI interface on/off",
    Default = Nebula.Keybinds.ToggleUI,
    }, function()
        if getgenv().Nebula_GUI then
            getgenv().Nebula_GUI:Clone().Parent = game.SoundService
            getgenv().Nebula_GUI.Enabled = not getgenv().Nebula_GUI.Enabled
        else
            if NebulaInternal.Checks.Notifications then
                Libraries.Notification:message({
                    Title = "Nebula",
                    Description = "[<b>GUI Not Found!</b>]"
                })
            end
        end
    end
)

KeybindsSection:Keybind({
    Title = "CFrame Speed",
    Description = "WalkSpeed using CFrame",
    Default = Nebula.Keybinds.CFrameSpeed,
    }, function()
        local active = false
        
        local dir = {Left = false, Right = false, Forward = false, Backward = false}
        
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.W) then
            dir.Forward = true
        end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.A) then
            dir.Left = true
        end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.S) then
            dir.Backpack = true
        end
        if Services.UserInputService:IsKeyDown(Enum.KeyCode.D) then
            dir.Right = true
        end
            
        local character = Vars.LocalPlayer.Character
        local Root = character:WaitForChild("HumanoidRootPart")
        if character and character:FindFirstChildOfClass("Humanoid") and character:FindFirstChildOfClass("Humanoid").Health ~= 0 then
            while task.wait() do
                if Services.UserInputService:IsKeyDown(NebulaInternal.Keybinds.CFrameSpeed) then
                    active = true
                    if active then
                        if dir.Forward then
                            Root.CFrame = Root.CFrame + (Root.CFrame.LookVector * (2))
                        end
                        if dir.Backward then
                            Root.CFrame = Root.CFrame + (-Root.CFrame.LookVector * (2))
                        end
                        if dir.Left then
                            Root.CFrame = Root.CFrame + (-Root.CFrame.RightVector * (2))
                        end
                        if dir.Right then
                            Root.CFrame = Root.CFrame + (Root.CFrame.RightVector * (2))
                        end
                    end
                else
                    active = false
                    dir.Forward = false
                    dir.Left = false
                    dir.Right = false
                    dir.Backward = false
                    return
                end
            end
        end
    end
)

KeybindsSection:Keybind({
    Title = "Freeze",
    Description = "Freezes the world and players on your client",
    Default = Nebula.Keybinds.Freeze,
    }, function()
        if settings().Network.IncomingReplicationLag == 1000 then
            settings().Network.IncomingReplicationLag = 0
            if NebulaInternal.Checks.Notifications then
                Libraries.Notification:message({
                    Title = "Nebula",
                    Description = "World has been <b><font color='rgb(173, 216, 230)'>unfrozen</font></b>",
                    Icon = 6023426926
                })
            end
        else
            settings().Network.IncomingReplicationLag = 1000
            if NebulaInternal.Checks.Notifications then
                Libraries.Notification:message({
                    Title = "Nebula",
                    Description = "World has been <b><font color='rgb(173, 216, 230)'>frozen</font></b>",
                    Icon = 6023426926
                })
            end
        end
    end
)

KeybindsSection:Keybind({
    Title = "Toggle ESP",
    Description = "Toggle ESP on/off",
    Default = Nebula.Keybinds.ToggleESP,
    }, function()
        NebulaInternal.ESP.Enabled = not NebulaInternal.ESP.Enabled
        getgenv().esp_stopped = not getgenv().esp_stopped
        if getgenv().esp_stopped == true then
            for _, Player in pairs(Services.Players:GetPlayers()) do
                if getgenv().PlayerDrawings[Player] then
                    for i, v in pairs(PlayerDrawings[Player]) do
                        if v then
                            v.Visible = false
                        end
                    end
                end
            end
            if NebulaInternal.Checks.Notifications then
                Libraries.Notification:message({
                    Title = "Nebula",
                    Description = "ESP has been <b><font color='rgb(255, 0, 0)'>disabled</font></b>",
                    Icon = 6023426926
                })
            end
        else
            for _, Player in pairs(Services.Players:GetPlayers()) do
                if getgenv().PlayerDrawings[Player] then
                    for i, v in pairs(PlayerDrawings[Player]) do
                        if v then
                            v.Visible = true
                        end
                    end
                end
            end
            if NebulaInternal.Checks.Notifications then
                Libraries.Notification:message({
                    Title = "Nebula",
                    Description = "ESP has been <b><font color='rgb(0, 255, 0)'>enabled</font></b>",
                    Icon = 6023426926
                })
            end
        end
    end
)

KeybindsSection:Keybind({
    Title = "Toggle Notifications",
    Description = "Toggle notifications on/off",
    Default = Nebula.Keybinds.Notifications,
    }, function()
        NebulaInternal.Checks.Notifications = not NebulaInternal.Checks.Notifications
        if NebulaInternal.Checks.Notifications then
            Libraries.Notification:message({
                Title = "Nebula",
                Description = "Notifications are now <b><font color='rgb(0, 255, 0)'>on</font></b>",
                Icon = 6023426926
            })
        else
            Libraries.Notification:message({
                Title = "Nebula",
                Description = "Notifications are now <b><font color='rgb(255, 0, 0)'>off</font></b>",
                Icon = 6023426926
            })
        end
    end
)

KeybindsSection:Keybind({
    Title = "Toggle Autoclick",
    Description = "Toggle autoclicker on/off",
    Default = Nebula.Keybinds.Autoclick,
    }, function()
        NebulaInternal.Checks.Autoclick = not NebulaInternal.Checks.Autoclick
        if NebulaInternal.Checks.Notifications then
            Libraries.Notification:message({
                Title = "Nebula",
                Description = "Autoclick is now <b>" .. tostring(NebulaInternal.Checks.Autoclick) .. "</b>",
                Icon = 6023426926
            })
        end
    end
)

ChecksSection:Textbox({
    Title = "Detection Distance",
    Description = "Radius for players to fire the checks",
    Default = tostring(Nebula.Checks.DetectionDistance)
    }, function(value)
        NebulaInternal.Checks.DetectionDistance = tonumber(value)
    end
)

ChecksSection:Toggle({
    Title = "Invisible Check",
    Description = "Does not resize for invisible players",
    Default = Nebula.Checks.InvisibleCheck
    }, function(value)
        NebulaInternal.Checks.InvisibleCheck = value
    end
)

ChecksSection:Toggle({
    Title = "Lunge Check",
    Description = "Only resizes on lunge",
    Default = Nebula.Checks.LungeCheck
    }, function(value)
        NebulaInternal.Checks.LungeCheck = value
    end
)

ChecksSection:Toggle({
    Title = "Fake Hitbox",
    Description = "Fake part for ingame hitboxes",
    Default = Nebula.Checks.FakeHitbox
    }, function(value)
        NebulaInternal.Checks.FakeHitbox = value
    end
)

ChecksSection:Toggle({
    Title = "Notifications",
    Description = "Notifies you of changes",
    Default = Nebula.Checks.Notifications
    }, function(value)
        NebulaInternal.Checks.Notifications = value
    end
)

ChecksSection:Toggle({
    Title = "Autoclick",
    Description = "Automatically clicks for you with half lunges for closets",
    Default = Nebula.Checks.Autoclick
    }, function(value)
        NebulaInternal.Checks.Autoclick = value
    end
)

ChecksSection:Toggle({
    Title = "Autoclick Icon",
    Description = "Adds the reloading mouse icon on lunge",
    Default = Nebula.Checks.AutoclickFakeIcon
    }, function(value)
        NebulaInternal.Checks.AutoclickFakeIcon = value
    end
)

ESPSection:Toggle({
    Title = "Enabled",
    Description = "Stream-Proof ESP",
    Default = Nebula.ESP.Enabled
    }, function(value)
        NebulaInternal.ESP.Enabled = value
        getgenv().esp_stopped = not getgenv().esp_stopped
        if getgenv().esp_stopped == true then
            for _, Player in pairs(Services.Players:GetPlayers()) do
                if getgenv().PlayerDrawings and getgenv().PlayerDrawings[Player] then
                    for i, v in pairs(PlayerDrawings[Player]) do
                        if v then
                            v.Visible = false
                        end
                    end
                end
            end
        else
            for _, Player in pairs(Services.Players:GetPlayers()) do
                if getgenv().PlayerDrawings and getgenv().PlayerDrawings[Player] then
                    for i, v in pairs(PlayerDrawings[Player]) do
                        if v then
                            v.Visible = true
                        end
                    end
                end
            end
        end
    end
)

if getgenv().Nebula_GUI then
    for i2, v2 in pairs(getgenv().Nebula_GUI:GetDescendants()) do
        if v2.Name == "Fill" or v2.Name == "Tab2" or v2.Name == "Texture" or v2.Name == "Line" then
            v2:Destroy()
        elseif v2.Name == "Watermark" then
            v2:Destroy()
        elseif v2.Name == "Main" and v2.Parent.Name == "Bar2Holder" then
            v2:Destroy()
        elseif v2.Name == "Content" and v2.Parent.Name == "MainUI" then
            v2.Position = UDim2.new(0.111, 0, 0, 0)
            v2.Size = UDim2.new(0.889, 0, 1, 0)
        elseif v2.Name == "Logo" then
            v2.Image = ""
        end
    end
end

----------------------------------------------------------------------------------------------------

local LocalPlayer = Vars.LocalPlayer

function Utility.New(Type, Outline, Name)
    local drawing = Drawing.new(Type)
    for i, v in pairs(Utility.Settings[Type]) do
        drawing[i] = v
    end
    if Outline then
        drawing.Color = Color3.fromRGB(0,0,0)
        drawing.Thickness = 3
    end
    return drawing
end
function Utility.Add(Player)
    if not PlayerDrawings[Player] then
        PlayerDrawings[Player] = {
            Offscreen = Utility.New("Triangle", nil, "Offscreen"),
            Name = Utility.New("Text", nil, "Name"),
            Team = Utility.New("Text", nil, "Team"),
            Distance = Utility.New("Text", nil, "Distance"),
            BoxOutline = Utility.New("Square", true, "BoxOutline"),
            Box = Utility.New("Square", nil, "Box"),
            HealthOutline = Utility.New("Line", true, "HealthOutline"),
            Health = Utility.New("Line", nil, "Health")
        }
    end
end

for _,Player in pairs(Services.Players:GetPlayers()) do
    if Player ~= LocalPlayer then
        Utility.Add(Player)
    end
end
Services.Players.PlayerAdded:Connect(Utility.Add)
Services.Players.PlayerRemoving:Connect(function(Player)
    if PlayerDrawings[Player] then
        for i,v in pairs(PlayerDrawings[Player]) do
            if v then
                v:Remove()
            end
        end

        PlayerDrawings[Player] = nil
    end
end)

local ESPLoop; ESPLoop = game:GetService("RunService").RenderStepped:Connect(function()
    if getgenv().esp_stopped then return end
    for _,Player in pairs (Services.Players:GetPlayers()) do
        local PlayerDrawing = PlayerDrawings[Player]
        if not PlayerDrawing then continue end

        for _,Drawing in pairs (PlayerDrawing) do
            Drawing.Visible = false
        end

        local Character = Player.Character
        local RootPart, Humanoid = Character and Character:FindFirstChild("HumanoidRootPart"), Character and Character:FindFirstChildOfClass("Humanoid")
        if not Character or not RootPart or not Humanoid then continue end

        local DistanceFromCharacter = (Vars.Camera.CFrame.Position - RootPart.Position).Magnitude
        if 20000 < DistanceFromCharacter then continue end

        local Pos, OnScreen = Vars.Camera:WorldToViewportPoint(RootPart.Position)
        if not OnScreen then
            if Player.Team ~= LocalPlayer.Team then continue end
            if Player.Team == LocalPlayer.Team then continue end

            local RootPos = RootPart.Position
            local CameraVector = Vars.Camera.CFrame.Position
            local LookVector = Vars.Camera.CFrame.LookVector

            local Dot = LookVector:Dot(RootPart.Position - Vars.Camera.CFrame.Position)
            if Dot <= 0 then
                RootPos = (CameraVector + ((RootPos - CameraVector) - ((LookVector * Dot) * 1.01)))
            end

            local ScreenPos, OnScreen = Vars.Camera:WorldToScreenPoint(RootPos)
            if not OnScreen then
                local Drawing = PlayerDrawing.Offscreen
                local FOV     = 800 - 400
                local Size    = 15

                local Center = (Vars.Camera.ViewportSize / 2)
                local Direction = (Vector2.new(ScreenPos.X, ScreenPos.Y) - Center).Unit
                local Radian = math.atan2(Direction.X, Direction.Y)
                local Angle = (((math.pi * 2) / FOV) * Radian)
                local ClampedPosition = (Center + (Direction * math.min(math.abs(((Center.Y - FOV) / math.sin(Angle)) * FOV), math.abs((Center.X - FOV) / (math.cos(Angle)) / 2))))
                local Point = Vector2.new(math.floor(ClampedPosition.X - (Size / 2)), math.floor((ClampedPosition.Y - (Size / 2) - 15)))

                local function Rotate(point, center, angle)
                    angle = math.rad(angle)
                    local rotatedX = math.cos(angle) * (point.X - center.X) - math.sin(angle) * (point.Y - center.Y) + center.X
                    local rotatedY = math.sin(angle) * (point.X - center.X) + math.cos(angle) * (point.Y - center.Y) + center.Y

                    return Vector2.new(math.floor(rotatedX), math.floor(rotatedY))
                end

                local Rotation = math.floor(-math.deg(Radian)) - 47
                Drawing.PointA = Rotate(Point + Vector2.new(Size, Size), Point, Rotation)
                Drawing.PointB = Rotate(Point + Vector2.new(-Size, -Size), Point, Rotation)
                Drawing.PointC = Rotate(Point + Vector2.new(-Size, Size), Point, Rotation)
                Drawing.Color = Player.Team ~= LocalPlayer.Team and Player.TeamColor.Color

                Drawing.Filled = not table.find({"outline", "blinking"}, "outline") and true or false
                if table.find({"outline", "blinking"}, "blinking") then
                    Drawing.Transparency = (math.sin(tick() * 5) + 1) / 2
                else
                    Drawing.Transparency = 1
                end

                Drawing.Visible = true
            end
        else
            local Size           = (Vars.Camera:WorldToViewportPoint(RootPart.Position - Vector3.new(0, 3, 0)).Y - Vars.Camera:WorldToViewportPoint(RootPart.Position + Vector3.new(0, 2.6, 0)).Y) / 2
            local BoxSize        = Vector2.new(math.floor(Size * 1.5), math.floor(Size * 1.9))
            local BoxPos         = Vector2.new(math.floor(Pos.X - Size * 1.5 / 2), math.floor(Pos.Y - Size * 1.6 / 2))

            local Name           = PlayerDrawing.Name
            local Team           = PlayerDrawing.Team
            local Distance       = PlayerDrawing.Distance
            local Box            = PlayerDrawing.Box
            local BoxOutline     = PlayerDrawing.BoxOutline
            local Health         = PlayerDrawing.Health
            local HealthOutline  = PlayerDrawing.HealthOutline

            Box.Size = BoxSize
            Box.Position = BoxPos
            Box.Visible = true
            Box.Color = Player.TeamColor.Color
            BoxOutline.Size = BoxSize
            BoxOutline.Position = BoxPos
            BoxOutline.Visible = true

            Health.From = Vector2.new((BoxPos.X - 5), BoxPos.Y + BoxSize.Y)
            Health.To = Vector2.new(Health.From.X, Health.From.Y - (Humanoid.Health / Humanoid.MaxHealth) * BoxSize.Y)
            Health.Color = Color3.fromRGB(0, 255, 0)
            Health.Visible = true

            HealthOutline.From = Vector2.new(Health.From.X, BoxPos.Y + BoxSize.Y + 1)
            HealthOutline.To = Vector2.new(Health.From.X, (Health.From.Y - 1 * BoxSize.Y) -1)
            HealthOutline.Visible = true

            local function SurroundString(String, Add)
                return String
            end

            Name.Text = SurroundString(Player.Name, "[]")
            Name.Position = Vector2.new(BoxSize.X / 2 + BoxPos.X, BoxPos.Y - 16)
            Name.Color = Color3.fromRGB(255, 255, 255)
            Name.Font = Drawing.Fonts["Plex"]
            Name.Visible = true

            local BottomOffset = BoxSize.Y + BoxPos.Y + 1
            local CurrentHealth = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid") and tostring(Player.Character:FindFirstChildOfClass("Humanoid").Health) or "NULL"
            Team.Text = tostring(math.floor(tonumber(CurrentHealth))) .. " hp"
            Team.Position = Vector2.new(BoxSize.X/2 + BoxPos.X, BottomOffset)
            Team.Color = Color3.fromRGB(0, 255, 0)
            Team.Font = Drawing.Fonts["Plex"]
            Team.Visible = true
            BottomOffset = BottomOffset + 15
            Distance.Text = SurroundString(math.floor(DistanceFromCharacter).."m", "[]")
            Distance.Position = Vector2.new(BoxSize.X/2 + BoxPos.X, BottomOffset)
            Distance.Color = Color3.fromRGB(255, 255, 255)
            Distance.Font = Drawing.Fonts["Plex"]
            Distance.Visible = true

            BottomOffset = BottomOffset + 15
        end
    end
end)

if getgenv().esp_stopped == true then
    for _, Player in pairs(Services.Players:GetPlayers()) do
        if getgenv().PlayerDrawings[Player] then
            for i, v in pairs(PlayerDrawings[Player]) do
                if v then
                    v.Visible = false
                end
            end
        end
    end
else
    for _, Player in pairs(Services.Players:GetPlayers()) do
        if getgenv().PlayerDrawings[Player] then
            for i, v in pairs(PlayerDrawings[Player]) do
                if v then
                    v.Visible = true
                end
            end
        end
    end
end

Services.RunService.Heartbeat:Connect(function()
    if NebulaInternal.Toggled then
        if Handle then
            --print(tostring(Handle.Size))
            if NebulaInternal.Size.Massless then
                for i, v in pairs(GetFakeHandles()) do
                    v.Massless = true
                end
                Handle.Massless = true
            else
                for i, v in pairs(GetFakeHandles()) do
                    v.Massless = OriginalHandleProperties.Massless
                end
                Handle.Massless = OriginalHandleProperties.Massless
            end

            if NebulaInternal.DamageAmplifier then
                Handle.CanQuery = not Handle.CanQuery
            end

            if NebulaInternal.Checks.FakeHitbox then
                if Vars.LocalPlayer.Character and Vars.LocalPlayer.Character:FindFirstChildWhichIsA("SelectionBox", true) and FakeHitbox then
                    local RealHitbox = Vars.LocalPlayer.Character:FindFirstChildWhichIsA("SelectionBox", true)
                    if RealHitbox.Adornee ~= FakeHitbox.Hitbox then
                        RealHitbox.Adornee = FakeHitbox.Hitbox
                    end
                    if FakeHitbox.Hitbox.Parent ~= Services.Workspace.Terrain then
                        FakeHitbox.Hitbox.Parent = Services.Workspace.Terrain
                    end
                    if FakeHitbox.Hitbox.CFrame ~= Handle.CFrame then
                        FakeHitbox.Hitbox.CFrame = Handle:GetRenderCFrame()
                    end
                    if FakeHitbox.Weld.Part1 ~= Handle then
                        FakeHitbox.Weld.Part1 = Handle
                    end
                end
            else
                if Vars.LocalPlayer.Character:FindFirstChildWhichIsA("SelectionBox", true) then
                    local RealHitbox = Vars.LocalPlayer.Character:FindFirstChildWhichIsA("SelectionBox", true)
                    if RealHitbox.Adornee ~= Handle then
                        RealHitbox.Adornee = Handle
                    end
                end
                if FakeHitbox and FakeHitbox.Weld and FakeHitbox.Weld.Part1 ~= nil then
                    FakeHitbox.Weld.Part1 = nil
                end
            end

            if NebulaInternal.Visualizer.Enabled then
                if Handle.Parent and Handle.Parent.Parent and Handle.Parent.Parent:IsA("Model") then
                    if Visualizers[1].Color ~= NebulaInternal.Visualizer.OutlineColour then
                        Visualizers[1].Color = NebulaInternal.Visualizer.OutlineColour
                    end
                    if Visualizers[2].Color ~= NebulaInternal.Visualizer.Colour then
                        Visualizers[2].Color = NebulaInternal.Visualizer.Colour
                    end

                    if NebulaInternal.Visualizer.Shape == "Box" then
                        NebulaInternal.Visualizer.Shape = "Block"
                    elseif NebulaInternal.Visualizer.Shape == "Sphere" then
                        NebulaInternal.Visualizer.Shape = "Ball"
                    end
                    if Visualizers[1].Shape ~= Enum.PartType[NebulaInternal.Visualizer.Shape] then
                        Visualizers[1].Shape = Enum.PartType[NebulaInternal.Visualizer.Shape]
                    end
                    if Visualizers[2].Shape ~= Enum.PartType[NebulaInternal.Visualizer.Shape] then
                        Visualizers[2].Shape = Enum.PartType[NebulaInternal.Visualizer.Shape]
                    end

                    if Visualizers[1].Size ~= Handle.Size then
                        Visualizers[1].Size = Handle.Size
                    end
                    if Visualizers[2].Size ~= Handle.Size then
                        Visualizers[2].Size = Handle.Size
                    end

                    if Visualizers[1].Parent ~= Services.Workspace.Terrain then
                        Visualizers[1].Parent = Services.Workspace.Terrain
                    end
                    if Visualizers[2].Parent ~= Services.Workspace.Terrain then
                        Visualizers[2].Parent = Services.Workspace.Terrain
                    end

                    Visualizers[1].CFrame = Handle:GetRenderCFrame()
                    Welds[1].Part0 = Visualizers[1]
                    Welds[1].Part1 = Handle
                    
                    Visualizers[2].CFrame = Handle:GetRenderCFrame()
                    Welds[2].Part0 = Visualizers[2]
                    Welds[2].Part1 = Handle
                else
                    if Visualizers[1].Parent ~= nil then
                        Visualizers[1].Parent = nil
                    end
                    if Visualizers[2].Parent ~= nil then
                        Visualizers[2].Parent = nil
                    end

                    if Welds[1].Part0 ~= nil then
                        Welds[1].Part0 = nil
                    end
                    if Welds[1].Part1 ~= nil then
                        Welds[1].Part1 = nil
                    end

                    if Welds[2].Part0 ~= nil then
                        Welds[2].Part0 = nil
                    end
                    if Welds[2].Part1 ~= nil then
                        Welds[2].Part1 = nil
                    end
                end
            else
                if Visualizers[1].Parent ~= nil then
                    Visualizers[1].Parent = nil
                end
                if Visualizers[2].Parent ~= nil then
                    Visualizers[2].Parent = nil
                end

                if Welds[1].Part0 ~= nil then
                    Welds[1].Part0 = nil
                end
                if Welds[1].Part1 ~= nil then
                    Welds[1].Part1 = nil
                end

                if Welds[2].Part0 ~= nil then
                    Welds[2].Part0 = nil
                end
                if Welds[2].Part1 ~= nil then
                    Welds[2].Part1 = nil
                end
            end

            if NebulaInternal.Checks.InvisibleCheck then
                for i, v in pairs(Services.Players:GetPlayers()) do
                    if v ~= Vars.LocalPlayer then
                        if v.Character and v.Character:FindFirstChild("Head") and v.Character:FindFirstChild("Torso") then
                            if v:DistanceFromCharacter(Handle.Position) <= NebulaInternal.Checks.DetectionDistance then
                                if v.Character.Torso.Transparency >= 0.7 then
                                    if Handle.Size ~= OriginalHandleProperties.Size then
                                        HandleGPCSConnections(Handle, "Size", "Disable")
                                        HandleConnections(Handle, "Changed", "Disable")
                                        HandleConnections(game, "ItemChanged", "Disable")
                                        for _, FakeHandle in pairs(GetFakeHandles()) do
                                            HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                            HandleConnections(FakeHandle, "Changed", "Disable")
                                        end

                                        Handle.Size = OriginalHandleProperties.Size
                                        for _, FakeHandle in pairs(GetFakeHandles()) do
                                            FakeHandle.Size = OriginalHandleProperties.Size
                                        end

                                        HandleGPCSConnections(Handle, "Size", "Enable")
                                        HandleConnections(Handle, "Changed", "Enable")
                                        HandleConnections(game, "ItemChanged", "Enable")
                                        for _, FakeHandle in pairs(GetFakeHandles()) do
                                            HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                            HandleConnections(FakeHandle, "Changed", "Enable")
                                        end
                                    end
                                else
                                    if NebulaInternal.Checks.LungeCheck then
                                        if Handle.Parent and Handle.Parent.GripUp.Z == 0 then
                                            if Handle.Size ~= Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) then
                                                HandleGPCSConnections(Handle, "Size", "Disable")
                                                HandleConnections(Handle, "Changed", "Disable")
                                                HandleConnections(game, "ItemChanged", "Disable")
                                                for _, FakeHandle in pairs(GetFakeHandles()) do
                                                    HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                                    HandleConnections(FakeHandle, "Changed", "Disable")
                                                end

                                                Handle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                                for _, FakeHandle in pairs(GetFakeHandles()) do
                                                    FakeHandle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                                end

                                                HandleGPCSConnections(Handle, "Size", "Enable")
                                                HandleConnections(Handle, "Changed", "Enable")
                                                HandleConnections(game, "ItemChanged", "Enable")
                                                for _, FakeHandle in pairs(GetFakeHandles()) do
                                                    HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                                    HandleConnections(FakeHandle, "Changed", "Enable")
                                                end
                                            end
                                        else
                                            if Handle.Size ~= OriginalHandleProperties.Size then
                                                HandleGPCSConnections(Handle, "Size", "Disable")
                                                HandleConnections(Handle, "Changed", "Disable")
                                                HandleConnections(game, "ItemChanged", "Disable")
                                                for _, FakeHandle in pairs(GetFakeHandles()) do
                                                    HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                                    HandleConnections(FakeHandle, "Changed", "Disable")
                                                end

                                                Handle.Size = OriginalHandleProperties.Size
                                                for _, FakeHandle in pairs(GetFakeHandles()) do
                                                    FakeHandle.Size = OriginalHandleProperties.Size
                                                end

                                                HandleGPCSConnections(Handle, "Size", "Enable")
                                                HandleConnections(Handle, "Changed", "Enable")
                                                HandleConnections(game, "ItemChanged", "Enable")
                                                for _, FakeHandle in pairs(GetFakeHandles()) do
                                                    HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                                    HandleConnections(FakeHandle, "Changed", "Enable")
                                                end
                                            end
                                        end
                                    else
                                        if Handle.Size ~= Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) then
                                            HandleGPCSConnections(Handle, "Size", "Disable")
                                            HandleConnections(Handle, "Changed", "Disable")
                                            HandleConnections(game, "ItemChanged", "Disable")
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                                HandleConnections(FakeHandle, "Changed", "Disable")
                                            end

                                            Handle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                FakeHandle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                            end
                                            
                                            HandleGPCSConnections(Handle, "Size", "Enable")
                                            HandleConnections(Handle, "Changed", "Enable")
                                            HandleConnections(game, "ItemChanged", "Enable")
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                                HandleConnections(FakeHandle, "Changed", "Enable")
                                            end
                                        end
                                    end
                                end
                            else
                                if NebulaInternal.Checks.LungeCheck then
                                    if Handle.Parent and Handle.Parent.GripUp.Z == 0 then
                                        if Handle.Size ~= Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) then
                                            HandleGPCSConnections(Handle, "Size", "Disable")
                                            HandleConnections(Handle, "Changed", "Disable")
                                            HandleConnections(game, "ItemChanged", "Disable")
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                                HandleConnections(FakeHandle, "Changed", "Disable")
                                            end

                                            Handle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                FakeHandle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                            end
                                            
                                            HandleGPCSConnections(Handle, "Size", "Enable")
                                            HandleConnections(Handle, "Changed", "Enable")
                                            HandleConnections(game, "ItemChanged", "Enable")
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                                HandleConnections(FakeHandle, "Changed", "Enable")
                                            end
                                        end
                                    else
                                        if Handle.Size ~= OriginalHandleProperties.Size then
                                            HandleGPCSConnections(Handle, "Size", "Disable")
                                            HandleConnections(Handle, "Changed", "Disable")
                                            HandleConnections(game, "ItemChanged", "Disable")
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                                HandleConnections(FakeHandle, "Changed", "Disable")
                                            end

                                            Handle.Size = OriginalHandleProperties.Size
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                FakeHandle.Size = OriginalHandleProperties.Size
                                            end

                                            HandleGPCSConnections(Handle, "Size", "Enable")
                                            HandleConnections(Handle, "Changed", "Enable")
                                            HandleConnections(game, "ItemChanged", "Enable")
                                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                                HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                                HandleConnections(FakeHandle, "Changed", "Enable")
                                            end
                                        end
                                    end
                                else
                                    if Handle.Size ~= Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) then
                                        HandleGPCSConnections(Handle, "Size", "Disable")
                                        HandleConnections(Handle, "Changed", "Disable")
                                        HandleConnections(game, "ItemChanged", "Disable")
                                        for _, FakeHandle in pairs(GetFakeHandles()) do
                                            HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                            HandleConnections(FakeHandle, "Changed", "Disable")
                                        end

                                        Handle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                        for _, FakeHandle in pairs(GetFakeHandles()) do
                                            FakeHandle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                                        end

                                        HandleGPCSConnections(Handle, "Size", "Enable")
                                        HandleConnections(Handle, "Changed", "Enable")
                                        HandleConnections(game, "ItemChanged", "Enable")
                                        for _, FakeHandle in pairs(GetFakeHandles()) do
                                            HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                            HandleConnections(FakeHandle, "Changed", "Enable")
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            else
                if NebulaInternal.Checks.LungeCheck then
                    if Handle.Parent and Handle.Parent.GripUp.Z == 0 then
                        if Handle.Size ~= Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) then
                            HandleGPCSConnections(Handle, "Size", "Disable")
                            HandleConnections(Handle, "Changed", "Disable")
                            HandleConnections(game, "ItemChanged", "Disable")
                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                HandleConnections(FakeHandle, "Changed", "Disable")
                            end

                            Handle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                FakeHandle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                            end

                            HandleGPCSConnections(Handle, "Size", "Enable")
                            HandleConnections(Handle, "Changed", "Enable")
                            HandleConnections(game, "ItemChanged", "Enable")
                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                HandleConnections(FakeHandle, "Changed", "Enable")
                            end
                        end
                    else
                        if Handle.Size ~= OriginalHandleProperties.Size then
                            HandleGPCSConnections(Handle, "Size", "Disable")
                            HandleConnections(Handle, "Changed", "Disable")
                            HandleConnections(game, "ItemChanged", "Disable")
                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                HandleGPCSConnections(FakeHandle, "Size", "Disable")
                                HandleConnections(FakeHandle, "Changed", "Disable")
                            end

                            Handle.Size = OriginalHandleProperties.Size
                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                FakeHandle.Size = OriginalHandleProperties.Size
                            end

                            HandleGPCSConnections(Handle, "Size", "Enable")
                            HandleConnections(Handle, "Changed", "Enable")
                            HandleConnections(game, "ItemChanged", "Enable")
                            for _, FakeHandle in pairs(GetFakeHandles()) do
                                HandleGPCSConnections(FakeHandle, "Size", "Enable")
                                HandleConnections(FakeHandle, "Changed", "Enable")
                            end
                        end
                    end
                else
                    if Handle.Size ~= Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z) then
                        HandleGPCSConnections(Handle, "Size", "Disable")
                        HandleConnections(Handle, "Changed", "Disable")
                        HandleConnections(game, "ItemChanged", "Disable")
                        for _, FakeHandle in pairs(GetFakeHandles()) do
                            HandleGPCSConnections(FakeHandle, "Size", "Disable")
                            HandleConnections(FakeHandle, "Changed", "Disable")
                        end

                        Handle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                        for _, FakeHandle in pairs(GetFakeHandles()) do
                            FakeHandle.Size = Vector3.new(NebulaInternal.Size.X, NebulaInternal.Size.Y, NebulaInternal.Size.Z)
                        end

                        HandleGPCSConnections(Handle, "Size", "Enable")
                        HandleConnections(Handle, "Changed", "Enable")
                        HandleConnections(game, "ItemChanged", "Enable")
                        for _, FakeHandle in pairs(GetFakeHandles()) do
                            HandleGPCSConnections(FakeHandle, "Size", "Enable")
                            HandleConnections(FakeHandle, "Changed", "Enable")
                        end
                    end
                end
            end

            if NebulaInternal.Checks.Autoclick then
                if not Services.UserInputService:GetFocusedTextBox() and Handle.Parent and Handle.Parent.GripUp.Z ~= 0 and Handle.Parent.Parent and Handle.Parent.Parent == Vars.LocalPlayer.Character and WindowFocused then
                    for _, player in next, Services.Players:GetPlayers() do
                        if player ~= Vars.LocalPlayer and player.Character and player.Character:FindFirstChildOfClass("Humanoid") and player.Character:FindFirstChild("Head") then
                            local Humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                            local Torso = player.Character:FindFirstChild("Torso")
                            if Humanoid.Health > 0 and Humanoid.MaxHealth <= 100 and Torso.Transparency < 0.8 then
                                local Dist = Vars.LocalPlayer:DistanceFromCharacter(Torso:GetRenderCFrame().Position)
                                if Dist > 25 and Dist < 40 then
                                    ActivateTool(Handle.Parent, "Half Lunge")
                                    if NebulaInternal.Checks.AutoclickFakeIcon then
                                        Vars.Mouse.Icon = "rbxasset://textures\\GunWaitCursor.png"
                                        task.delay(0.1, function()
                                            if Vars.LocalPlayer.Character:FindFirstChildOfClass("Tool") then
                                                Vars.Mouse.Icon = "rbxasset://textures\\GunCursor.png"
                                            end
                                        end)
                                    end
                                elseif Dist < 25 then
                                    ActivateTool(Handle.Parent, "Lunge")
                                    if NebulaInternal.Checks.AutoclickFakeIcon then
                                        Vars.Mouse.Icon = "rbxasset://textures\\GunWaitCursor.png"
                                        task.delay(0.1, function()
                                            if Vars.LocalPlayer.Character:FindFirstChildOfClass("Tool") then
                                                Vars.Mouse.Icon = "rbxasset://textures\\GunCursor.png"
                                            end
                                        end)
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end
    else
        NebulaInternal.ESP.Enabled = false
        getgenv().esp_stopped = false
        if getgenv().esp_stopped == true then
            for _, Player in pairs(Services.Players:GetPlayers()) do
                if getgenv().PlayerDrawings[Player] then
                    for i, v in pairs(PlayerDrawings[Player]) do
                        if v then
                            v.Visible = false
                        end
                    end
                end
            end
        else
            for _, Player in pairs(Services.Players:GetPlayers()) do
                if getgenv().PlayerDrawings[Player] then
                    for i, v in pairs(PlayerDrawings[Player]) do
                        if v then
                            v.Visible = true
                        end
                    end
                end
            end
        end

        if Handle.Size ~= OriginalHandleProperties.Size then
            HandleGPCSConnections(Handle, "Size", "Disable")
            HandleConnections(Handle, "Changed", "Disable")
            HandleConnections(game, "ItemChanged", "Disable")
            for _, FakeHandle in pairs(GetFakeHandles()) do
                HandleGPCSConnections(FakeHandle, "Size", "Disable")
                HandleConnections(FakeHandle, "Changed", "Disable")
            end

            Handle.Size = OriginalHandleProperties.Size
            for _, FakeHandle in pairs(GetFakeHandles()) do
                FakeHandle.Size = OriginalHandleProperties.Size
            end

            HandleGPCSConnections(Handle, "Size", "Enable")
            HandleConnections(Handle, "Changed", "Enable")
            HandleConnections(game, "ItemChanged", "Enable")
            for _, FakeHandle in pairs(GetFakeHandles()) do
                HandleGPCSConnections(FakeHandle, "Size", "Enable")
                HandleConnections(FakeHandle, "Changed", "Enable")
            end
        end
    end
end)
