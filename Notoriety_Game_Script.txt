local Finity = loadstring(game:HttpGet('https://pastebin.com/raw/N0RbJ8Yx'))();

local window = Finity.new(true);
window.ChangeToggleKey(Enum.KeyCode.P);

do -- Main Category
    local mainCategory = window:Category('Main');

    local damageRemote = game:GetService('ReplicatedStorage').RS_Package.Assets.Remotes.Damage;

    local remoteKey;

    local backpack = game:GetService('Players').LocalPlayer.Backpack;

    local pos = Vector3.new();

    local function Damage(char, godmode)
        pcall(function()
            coroutine.wrap(function()
                damageRemote:FireServer('Damage', remoteKey, char:WaitForChild('Humanoid', 10), godmode and -9e9 or 9e9, char:WaitForChild('Head', 5), backpack:GetChildren()[1].Name, pos);
            end)();
        end);
    end;

    do -- Get Remote Key
        local scr = game:GetService('Players').LocalPlayer.PlayerScripts.SPS_Package.LocalGuns;

        for i, v in next, getgc() do
            if type(v) == 'function' and getfenv(v).script == scr then
                for i, v in next, getupvalues(v) do
                    if i == 17 and type(v) == 'string' then
                        remoteKey = v;
                        break;
                    end;
                end;
            end;
        end;
    end;

    do -- Police Sector
        local policeSector = mainCategory:Sector('Police');

        local police = workspace.Police;

        local killAllPoliceConnection;

        local function KillAllPoliceOnce()
            for i, v in next, police:GetChildren() do
                Damage(v);
            end;
        end;

        policeSector:Cheat('Button', 'Kill all police [once]', KillAllPoliceOnce, {text = 'Kill'});

        policeSector:Cheat('Checkbox', 'Kill all police', function(enabled)
            if enabled then
                KillAllPoliceOnce();
                killAllPoliceConnection = police.ChildAdded:Connect(function(char)
                    Damage(char);
                end);
            else
                killAllPoliceConnection:Disconnect();
                killAllPoliceConnection = nil;
            end;
        end);
    end;

    do -- Criminals Sector
        local criminalsSector = mainCategory:Sector('Criminals');

        local criminals = workspace.Criminals;

        criminalsSector:Cheat('Textbox', 'God', function(text)
            local text = text:lower();
            if text == 'all' then
                for i, v in next, criminals:GetChildren() do
                    Damage(v, true);
                end;
            else
                for i, v in next, criminals:GetChildren() do
                    if v.Name:lower():find(text) then
                        Damage(v, true);
                    end;
                end;
            end;
        end, {placeholder = 'Username'});

        criminalsSector:Cheat('Textbox', 'Kill', function(text)
            local text = text:lower();
            if text == 'all' then
                for i, v in next, criminals:GetChildren() do
                    Damage(v);
                end;
            else
                for i, v in next, criminals:GetChildren() do
                    if v.Name:lower():find(text) then
                        Damage(v);
                    end;
                end;
            end;
        end, {placeholder = 'Username'});
    end;

    do -- Citizens Sector
        local citizensSector = mainCategory:Sector('Citizens');

        local citizens = workspace.Citizens;

        local yellRemote = game:GetService('ReplicatedStorage').RS_Package.Remotes.PlayerYell;

        citizensSector:Cheat('Button', 'Yell at all citizens', function()
            yellRemote:FireServer(citizens:GetChildren());
        end, {text = 'Yell'});
    end;

    do -- Misc Sector
        local miscSector = mainCategory:Sector('Misc');

        local cameras = workspace.Cameras;

        local hitRemote = game:GetService('ReplicatedStorage').RS_Package.Assets.Remotes.HitObject;

	-- Instant interactions is kinda glitchy

        --[[local mt = getrawmetatable(game);
        local oldNamecall = mt.__namecall;

        miscSector:Cheat('Checkbox', 'Instant interactions', function(enabled)
            if enabled then
                setreadonly(mt, false);
                mt.__namecall = newcclosure(function(self, ...)
                    if getnamecallmethod() == 'FireServer' and self.Name == 'StartInteraction' then
                        return oldNamecall(game.GetService(game, 'ReplicatedStorage').RS_Package.Remotes.CompleteInteraction, ...);
                    end;

                    return oldNamecall(self, ...);
                end);
                setreadonly(mt, true);
            else
                setreadonly(mt, false);
                mt.__namecall = oldNamecall;
                setreadonly(mt, true);
            end;
        end, {text = 'Destroy'});]]

        miscSector:Cheat('Button', 'Destroy all cameras', function()
            for i, v in next, cameras:GetChildren() do
                hitRemote:FireServer(remoteKey, v.Hitbox, false, nil, nil, pos);
            end;
        end, {text = 'Destroy'});

        miscSector:Cheat('Label', 'if ur cursor is stuck then press P');
    end;
end;