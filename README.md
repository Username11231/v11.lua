local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Menu " .. Fluent.Version,
    SubTitle = "КАНАВА СКРИПТ",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true, -- The blur may be detectable, setting this to false disables blur entirely
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl -- Used when theres no MinimizeKeybind
})

--Fluent provides Lucide Icons https://lucide.dev/icons/ for the tabs, icons are optional
local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "" }),
    Character = Window:AddTab({ Title = "Character", Icon = "" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do



    Tabs.Main:AddButton({
        Title = "АНТИ-ЛЮКИ",
        Description = "Удаляет люки",
        Callback = function()
            Window:Dialog({
                Title = "Подтверждение",
                Content = "Вы уверены?",
                Buttons = {
                    {
                        Title = "Confirm",
                        Callback = function()
                            game.Workspace.luks:Destroy()
                        end
                    },
                    {
                        Title = "Cancel",
                        Callback = function()
                            print("Cancelled the dialog.")
                        end
                    }
                }
            })
        end
    })
    
    
    

Tabs.Main:AddButton({
        Title = "АНТИ-ОПАСНЫЕ-ТРИГГЕРЫ",
        Description = "Не даёт вам умереть о опасные триггеры, или получить урон",
        Callback = function()
            Window:Dialog({
                Title = "Подтверждение",
                Content = "Вы уверены?",
                Buttons = {
                    {
                        Title = "Confirm",
                        Callback = function()
                            game.Workspace.FireParts:Destroy()
                            game.Workspace.RagdollParts:Destroy()
                            game.Workspace.KillParts:Destroy()
                        end
                    },
                    {
                        Title = "Cancel",
                        Callback = function()
                            print("Cancelled the dialog.")
                        end
                    }
                }
            })
        end
    })

Tabs.Main:AddButton({
        Title = "Супер-скорость",
        Description = "Ускоряет вас, до 60 км/ч",
        Callback = function()
            Window:Dialog({
                Title = "Подтверждение",
                Content = "Вы уверены?",
                Buttons = {
                    {
                        Title = "Ускорить",
                        Callback = function()
                            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 60
                        end
                    },
                    {
                        Title = "Вернуть до 16",
                        Callback = function()
                            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16
                        end
                    }
                }
            })
        end
    })
    
Tabs.Main:AddButton({
        Title = "Супер-прыжок",
        Description = "Усиливает ваш прыжок, до 95",
        Callback = function()
            Window:Dialog({
                Title = "Подтверждение",
                Content = "Вы уверены?",
                Buttons = {
                    {
                        Title = "Усилить",
                        Callback = function()
                        	game.Players.LocalPlayer.Character.Humanoid.UseJumpPower = true
                            game.Players.LocalPlayer.Character.Humanoid.JumpPower = 95
                        end
                    },
                    {
                        Title = "Вернуть до 50",
                        Callback = function()
                            game.Players.LocalPlayer.Character.Humanoid.JumpPower = 50
                        end
                    }
                }
            })
        end
    })

local Toggle = Tabs.Character:AddToggle("MyToggles", {Title = "Скорость", Default = false })

    Toggle:OnChanged(function()
        while Options.MyToggles.Value do
        wait(0.15)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 50
        end
    end)
	
	Options.MyToggles:SetValue(false)

local Toggle = Tabs.Main:AddToggle("MyToggle", {Title = "Спам ударами", Default = false })

    Toggle:OnChanged(function()
        while Options.MyToggle.Value do
        wait(0.1)
        game.ReplicatedStorage.Remotes.HitRequest:InvokeServer(true)
        end
    end)

    
    Options.MyToggle:SetValue(false)

    local Slider = Tabs.Main:AddSlider("Slider", {
        Title = "Гравитация",
        Description = "Меняет вашу гравитацию",
        Default = 2,
        Min = 196.2,
        Max = 1000,
        Rounding = 1,
        Callback = function(Value)
            game.Workspace.Gravity = Value
        end
    })

    Slider:OnChanged(function(Value)
        print("Slider changed:", Value)
    end)

    Slider:SetValue(3)



    local Input = Tabs.Main:AddInput("Input", {
        Title = "Введите сутки",
        Default = "Default",
        Placeholder = "От 0, до 23",
        Numeric = true, -- Only allows numbers
        Finished = false, -- Only calls callback when you press enter
        Callback = function(Value)
            game.Lighting.ClockTime = Value
        end
    })

    Input:OnChanged(function()
        print("Input updated:", Input.Value)
    end)
end


-- Addons:
-- SaveManager (Allows you to have a configuration system)
-- InterfaceManager (Allows you to have a interface managment system)

-- Hand the library over to our managers
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)

-- Ignore keys that are used by ThemeManager.
-- (we dont want configs to save themes, do we?)
SaveManager:IgnoreThemeSettings()

-- You can add indexes of elements the save manager should ignore
SaveManager:SetIgnoreIndexes({})

-- use case for doing it this way:
-- a script hub could have themes in a global folder
-- and game configs in a separate folder per game
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")

InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)


Window:SelectTab(1)

Fluent:Notify({
    Title = "Уведомление",
    Content = "Скрипт успешно загружен.",
    Duration = 8
})

-- You can use the SaveManager:LoadAutoloadConfig() to load a config
-- which has been marked to be one that auto loads!
SaveManager:LoadAutoloadConfig()
