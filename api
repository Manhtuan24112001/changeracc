-- 🌿 CLEAN WORLD & KEEP LOCAL PLAYER ONLY
-- by ChatGPT (optimized)

local Players = game:GetService('Players')
local player = Players.LocalPlayer

-- ⚙️ Cấu hình: Bật/Tắt tính năng xoá player khác
local KEEP_ONLY_LOCALPLAYER = true

-- 🧍‍♂️ Hàm xoá tất cả player khác
local function removeOtherPlayers()
    if not KEEP_ONLY_LOCALPLAYER then
        return
    end
    for _, plr in ipairs(Players:GetChildren()) do
        if plr ~= player then
            pcall(function()
                plr:Destroy()
                warn('🧹 Đã xoá player:', plr.Name)
            end)
        end
    end
end

-- 🚫 Khi có player mới join -> xoá ngay và quét lại toàn bộ
Players.PlayerAdded:Connect(function(plr)
    if not KEEP_ONLY_LOCALPLAYER then
        return
    end
    if plr ~= player then
        pcall(function()
            plr:Destroy()
            warn('🚫 Player mới bị xoá:', plr.Name)
        end)
    end
    -- kiểm tra lại toàn bộ danh sách
    removeOtherPlayers()
end)

-- 🧹 Khi có player rời -> đảm bảo danh sách sạch
Players.PlayerRemoving:Connect(function(_)
    task.defer(removeOtherPlayers)
end)

-- ⏳ Vòng kiểm tra liên tục để đảm bảo không lọt player ẩn
task.spawn(function()
    while task.wait(3) do
        removeOtherPlayers()
    end
end)

-- Chạy lần đầu tiên
removeOtherPlayers()

-- 📝 Danh sách đường dẫn thủ công muốn xoá
local ManualPathsToDelete = {
    'workspace.GardenCoinShop',
    'workspace.Debris',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.PotionShelf',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.WitchesBrewLeaderboard',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.WitchesBrewPlate',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.Witch.Broom',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.Casket',
    "workspace.Interaction.UpdateItems.HalloweenMarketEvent['Devious Pumpkin']",
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenEloise',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenLights',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenPlate',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenSteven',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.Model1',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.Model2',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.PumpkinLight',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.CandyCornSpecialCurrencyUIOverlap',
    'workspace.HalloweenOuter',
    'workspace.FlyBorder',
    'workspace.BlockPartyOuter',
}

-- 🌟 Ẩn phần hiển thị của Part nhưng giữ nguyên Prompt + thư mục con
local function hidePartVisualButKeepContents(part)
    part.Transparency = 1
    part.CastShadow = false
    part.CanCollide = false
    part.CanTouch = false
    part.CanQuery = false

    for _, child in ipairs(part:GetChildren()) do
        if
            child:IsA('SpecialMesh')
            or child:IsA('Decal')
            or child:IsA('MeshPart')
            or child:IsA('UnionOperation')
        then
            child:Destroy()
        end
    end
end

-- 🧹 Xoá phần hiển thị folder nhưng giữ Prompt
local function clearFolderVisualButKeepPrompt(folder)
    for _, desc in ipairs(folder:GetDescendants()) do
        if desc:IsA('BasePart') then
            local prompt = desc:FindFirstChildWhichIsA('ProximityPrompt')
            if prompt then
                hidePartVisualButKeepContents(desc)
            else
                desc:Destroy()
            end
        elseif
            desc:IsA('MeshPart')
            or desc:IsA('UnionOperation')
            or desc:IsA('Decal')
        then
            desc:Destroy()
        end
    end
end

-- 🌳 Xoá cây & quả
local function clearPlantAndFruits(plant)
    clearFolderVisualButKeepPrompt(plant)
    local fruits = plant:FindFirstChild('Fruits')
    if fruits then
        for _, fruit in ipairs(fruits:GetChildren()) do
            clearFolderVisualButKeepPrompt(fruit)
        end
    end
end

-- 🧼 Xoá đường dẫn thủ công
local function deleteManualPaths()
    for _, path in ipairs(ManualPathsToDelete) do
        local success, target = pcall(function()
            return loadstring('return ' .. path)()
        end)
        if success and target then
            warn('Xóa theo đường dẫn:', path)
            target:Destroy()
        end
    end
end

-- 🧹 Xoá plot người chơi khác
local function deleteOtherPlayerPlots()
    local plots = workspace:FindFirstChild('__THINGS')
        and workspace.__THINGS:FindFirstChild('Plots')
    if plots then
        for _, plot in ipairs(plots:GetChildren()) do
            local sign = plot:FindFirstChild('Build')
                and plot.Build:FindFirstChild('Sign')
            local isMine = false
            if sign then
                for _, gui in ipairs(sign:GetDescendants()) do
                    if gui:IsA('TextLabel') and gui.Text:find(player.Name) then
                        isMine = true
                        break
                    end
                end
            end
            if not isMine then
                plot:Destroy()
            end
        end
    end
end

-- 🧼 Vòng lặp dọn map cũ liên tục
task.spawn(function()
    while task.wait(2) do
        pcall(function()
            -- ❌ Xoá Lighting object
            for _, obj in ipairs(game:GetService('Lighting'):GetChildren()) do
                obj:Destroy()
            end

            -- ❌ Xoá Debris
            if workspace:FindFirstChild('Debris') then
                for _, obj in ipairs(workspace.Debris:GetChildren()) do
                    obj:Destroy()
                end
            end

            -- ❌ Xoá Terrain (giữ object Terrain nhưng xoá tất cả nội dung)
            if workspace:FindFirstChild('Terrain') then
                workspace.Terrain:Clear()
            end

            -- 🧼 Xoá theo đường dẫn thủ công
            deleteManualPaths()

            -- 🌾 Dọn toàn bộ Farm
            for _, farm in workspace:GetChildren() do
                if farm.Name == 'Farm' then
                    for _, subFarm in ipairs(farm:GetChildren()) do
                        if
                            subFarm.Name == 'Farm'
                            and subFarm:FindFirstChild('Important')
                            and subFarm.Important:FindFirstChild('Data')
                            and subFarm.Important.Data:FindFirstChild('Owner')
                        then
                            local isMine = (
                                subFarm.Important.Data.Owner.Value
                                == player.Name
                            )
                            if not isMine then
                                subFarm:Destroy()
                            else
                                local important = subFarm.Important
                                if
                                    important:FindFirstChild('Plants_Physical')
                                then
                                    for _, plant in
                                        ipairs(
                                            important.Plants_Physical:GetChildren()
                                        )
                                    do
                                        clearPlantAndFruits(plant)
                                    end
                                end
                                for _, folderName in ipairs({
                                    'Decorations',
                                    'Fences',
                                    'Cosmetics',
                                }) do
                                    if important:FindFirstChild(folderName) then
                                        for _, obj in
                                            ipairs(
                                                important[folderName]:GetChildren()
                                            )
                                        do
                                            obj:Destroy()
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end

            -- 🧹 Xoá plot người chơi khác
            deleteOtherPlayerPlots()
            -- 🧹 Xoá player khác (đảm bảo kiểm tra định kỳ)
            removeOtherPlayers()
        end)
    end
end)

print(
    '✅ Script hoàn thiện: Dọn map & chỉ giữ lại LocalPlayer 🌿✨'
)

-- 🎃 HALLOWEEN DASHBOARD (Overlay + Auto Upgrade + Currency + Toggle)
-- by GPT-5 | Simple, Clean & Functional

local Players = game:GetService('Players')
local Rep = game:GetService('ReplicatedStorage')
local Network = Rep:WaitForChild('Network')
local Save = require(Rep.Library.Client.Save)
local PURCHASE = Network:FindFirstChild('EventUpgrades: Purchase')

local player = Players.LocalPlayer
local gui = Instance.new('ScreenGui', player:WaitForChild('PlayerGui'))
gui.IgnoreGuiInset = true
gui.ResetOnSpawn = false
gui.DisplayOrder = 999 -- luôn hiển thị trên cùng
gui.Name = 'HalloweenDashboard'

-- ⚙️ Nâng cấp cấu hình
local AllUpgrades = {
    'HalloweenCandyMultiplier',
    'HalloweenTrickOrTreatLuck',
    'HalloweenHugeLuck',
    'HalloweenTitanicLuck',
    'HalloweenMoreDiamonds',
    'HalloweenMoreWitchHats',
    'HalloweenEggLuck',
}
local Priority = {
    'HalloweenMoreDiamonds',
    'HalloweenMoreWitchHats',
    'HalloweenCandyMultiplier',
}
local DELAY, RUN_AUTO = 1.5, true

-- 💰 Định dạng K / M / B
local function formatNumber(n)
    if not n then
        return '0'
    end
    if n >= 1e9 then
        return string.format('%.2fB', n / 1e9)
    elseif n >= 1e6 then
        return string.format('%.2fM', n / 1e6)
    elseif n >= 1e3 then
        return string.format('%.1fK', n / 1e3)
    else
        return tostring(math.floor(n))
    end
end

local function getData()
    return Save.Get() or {}
end
local function getUpgradeLevel(name)
    return (getData().EventUpgrades or {})[name] or 0
end
local function getCurrencies()
    local cur = (getData().Inventory and getData().Inventory.Currency) or {}
    local result = {}
    for _, info in pairs(cur) do
        result[info.id] = info._am or 0
    end
    return result
end

-- 🧱 Overlay nền đen có thể bật/tắt
local overlay = Instance.new('Frame', gui)
overlay.Size = UDim2.new(1, 0, 1, 0)
overlay.BackgroundColor3 = Color3.new(0, 0, 0)
overlay.BackgroundTransparency = 0.4
overlay.BorderSizePixel = 0

local toggleButton = Instance.new('TextButton', gui)
toggleButton.Size = UDim2.new(0, 50, 0, 50)
toggleButton.Position = UDim2.new(0, 10, 0.5, -25)
toggleButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
toggleButton.Text = 'MT'
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextSize = 20
toggleButton.ZIndex = 1000
toggleButton.MouseButton1Click:Connect(function()
    overlay.Visible = not overlay.Visible
end)

-- 🎃 UI chính
local main = Instance.new('Frame', overlay)
main.AnchorPoint = Vector2.new(0.5, 0.5)
main.Position = UDim2.new(0.5, 0, 0.5, 0)
main.Size = UDim2.new(0, 520, 0, 420)
main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
main.BackgroundTransparency = 0.3
main.BorderSizePixel = 0
main.ZIndex = 999

local title = Instance.new('TextLabel', main)
title.Size = UDim2.new(1, 0, 0, 50)
title.BackgroundTransparency = 1
title.Text = '🎃 HALLOWEEN DASHBOARD'
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(255, 170, 0)
title.TextSize = 28
title.ZIndex = 1000

local scroll = Instance.new('ScrollingFrame', main)
scroll.Size = UDim2.new(1, -20, 1, -60)
scroll.Position = UDim2.new(0, 10, 0, 60)
scroll.BackgroundTransparency = 1
scroll.CanvasSize = UDim2.new(0, 0, 0, 600)
scroll.ScrollBarThickness = 6
scroll.ZIndex = 999

local labels = { currencies = {}, upgrades = {} }
local currencyList = { 'Diamonds', 'HalloweenCoins', 'Coins' }

-- 💰 [CURRENCY]
local y = 0
local header1 = Instance.new('TextLabel', scroll)
header1.Text = '💰 [CURRENCY]'
header1.Size = UDim2.new(1, 0, 0, 30)
header1.BackgroundTransparency = 1
header1.TextColor3 = Color3.fromRGB(0, 255, 150)
header1.Font = Enum.Font.GothamBold
header1.TextSize = 28
header1.ZIndex = 999
y = 40

for _, name in ipairs(currencyList) do
    local lbl = Instance.new('TextLabel', scroll)
    lbl.Position = UDim2.new(0, 10, 0, y)
    lbl.Size = UDim2.new(1, -20, 0, 28)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.new(1, 1, 1)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 28
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Text = '• ' .. name .. ' = 0'
    lbl.ZIndex = 999
    labels.currencies[name] = lbl
    y += 32
end

-- ⚙️ [UPGRADES]
local header2 = Instance.new('TextLabel', scroll)
header2.Text = '⚙️ [UPGRADES]'
header2.Position = UDim2.new(0, 0, 0, y + 10)
header2.Size = UDim2.new(1, 0, 0, 30)
header2.BackgroundTransparency = 1
header2.TextColor3 = Color3.fromRGB(255, 200, 0)
header2.Font = Enum.Font.GothamBold
header2.TextSize = 28
header2.ZIndex = 999
y += 50

for _, name in ipairs(AllUpgrades) do
    local lbl = Instance.new('TextLabel', scroll)
    lbl.Position = UDim2.new(0, 10, 0, y)
    lbl.Size = UDim2.new(1, -20, 0, 28)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.new(1, 1, 1)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 28
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Text = '• ' .. name .. ' = 0/10'
    lbl.ZIndex = 999
    labels.upgrades[name] = lbl
    y += 32
end
scroll.CanvasSize = UDim2.new(0, 0, 0, y + 50)

-- 🔄 Cập nhật UI
local function updateUI()
    local currencies = getCurrencies()
    for _, name in ipairs(currencyList) do
        local lbl = labels.currencies[name]
        if lbl then
            lbl.Text = string.format(
                '• %-15s = %s',
                name,
                formatNumber(currencies[name] or 0)
            )
        end
    end
    local up = getData().EventUpgrades or {}
    for _, name in ipairs(AllUpgrades) do
        local lvl = up[name] or 0
        local lbl = labels.upgrades[name]
        if lbl then
            lbl.Text = string.format('• %-25s = %d/10', name, lvl)
            lbl.TextColor3 = lvl >= 10 and Color3.fromRGB(0, 255, 100)
                or Color3.new(1, 1, 1)
        end
    end
end

-- ⚡ Auto Upgrade
task.spawn(function()
    while task.wait(DELAY) do
        if not RUN_AUTO then
            continue
        end
        local done = true
        for _, n in ipairs(Priority) do
            if getUpgradeLevel(n) < 10 then
                done = false
                PURCHASE:InvokeServer(n)
            end
        end
        if done then
            local others = {}
            for _, n in ipairs(AllUpgrades) do
                if not table.find(Priority, n) and getUpgradeLevel(n) < 10 then
                    table.insert(others, n)
                end
            end
            if #others == 0 then
                break
            end
            PURCHASE:InvokeServer(others[math.random(1, #others)])
        end
        updateUI()
    end
end)

-- 🔁 Liên tục làm mới
task.spawn(function()
    while task.wait(2) do
        updateUI()
    end
end)

updateUI()

-- 🧩 AUTO HOUSE & EGG MANAGER - TỐC ĐỘ CAO (CẬP NHẬT: QUYẾT THEO SỐ TIỀN VÀ INCOME)
-- by ChatGPT (tùy chỉnh theo yêu cầu)

-- CONFIG
local PRINT_VERBOSE = true
local SIGN_RECHECK_INTERVAL = 10 -- giây, check sign liên tục
local EGG_DELAY = 0.25 -- delay giữa mỗi lần mua trứng (giây)
local MAX_EGG_SLOT = 5 -- số house/trứng tối đa (slot 1..MAX_EGG_SLOT)

local Players = game:GetService('Players')
local ReplicatedStorage = game:GetService('ReplicatedStorage')
local PLOTS = workspace:WaitForChild('__THINGS'):WaitForChild('Plots')
local Plots_Invoke = ReplicatedStorage:WaitForChild('Network')
    :WaitForChild('Plots_Invoke')
local LocalPlayer = Players.LocalPlayer

local SaveModule = ReplicatedStorage:FindFirstChild('Library')
    and ReplicatedStorage.Library:FindFirstChild('Client') -- optional
local Save = nil
pcall(function()
    Save = require(ReplicatedStorage.Library.Client.Save)
end)

-- Helper: chuyển "5.77k", "2.5m" => number
local function parseNumber(str)
    if not str then
        return nil
    end
    str = tostring(str):lower():gsub(',', ''):gsub('%s+', '')
    local n, suffix = str:match('([%d%.]+)([kmbt]?)')
    n = tonumber(n)
    if not n then
        return nil
    end
    local mult = { k = 1e3, m = 1e6, b = 1e9, t = 1e12 }
    return n * (mult[suffix] or 1)
end

-- Extract amount/sec từ text
local function extractAmount(text)
    if not text then
        return nil
    end
    local match = text:lower():match('([%d%.,]+%s*[kmbt]?)%s*/%s*s')
        or text:lower():match('([%d%.,]+%s*[kmbt]?)%s*per')
    if match then
        return parseNumber(match)
    end
    return parseNumber(text)
end

-- Lấy số Coins hiện có từ Save (fallback = 0)
local function getCurrentCoins()
    if not Save then
        return 0
    end
    local data = Save.Get()
    if not data or not data.Inventory or not data.Inventory.Currency then
        return 0
    end
    for _, c in pairs(data.Inventory.Currency) do
        if
            c.id and (c.id:lower():find('coin') or c.id:lower():find('coins'))
        then
            return tonumber(c._am) or 0
        end
        -- một số game dùng id "Coins" hoặc "Coin" hoặc "coins"
    end
    return 0
end

-- Tìm plot của mình + amount (/s)
local function findMyPlotAndAmount()
    for _, plot in pairs(PLOTS:GetChildren()) do
        local sign = plot:FindFirstChild('Build')
            and plot.Build:FindFirstChild('Sign')
        if sign then
            for _, gui in pairs(sign:GetDescendants()) do
                if gui:IsA('TextLabel') and gui.Text:find(LocalPlayer.Name) then
                    for _, t in pairs(sign:GetDescendants()) do
                        if
                            t:IsA('TextLabel')
                            and (
                                t.Text:find('/s') or t.Text:lower():find('per')
                            )
                        then
                            local amt = extractAmount(t.Text)
                            if PRINT_VERBOSE then
                                print(
                                    '✅ Tìm plot:',
                                    plot.Name,
                                    '| Amount/s:',
                                    amt
                                )
                            end
                            return plot, amt
                        end
                    end
                end
            end
        end
    end
    return nil, nil
end

-- Mở house
local function unlockHouse(plotId, houseId)
    local ok, res = pcall(function()
        return Plots_Invoke:InvokeServer(plotId, 'PurchaseHouse', houseId)
    end)
    if ok then
        print(('🏠 House #%d mở (resp=%s)'):format(houseId, tostring(res)))
    else
        warn(('⚠️ Lỗi mở House #%d: %s'):format(houseId, tostring(res)))
    end
end

-- Mua trứng (qty)
local function purchaseEgg(plotId, slot, qty)
    qty = qty or 1
    local ok, res = pcall(function()
        return Plots_Invoke:InvokeServer(plotId, 'PurchaseEgg', slot, qty)
    end)
    if ok then
        if PRINT_VERBOSE then
            print(
                ('✅ Mua x%d trứng slot #%d (resp=%s)'):format(
                    qty,
                    slot,
                    tostring(res)
                )
            )
        end
    else
        warn(
            ('⚠️ Lỗi mua trứng slot #%d qty=%d: %s'):format(
                slot,
                qty,
                tostring(res)
            )
        )
    end
end

-- THREAD CONTROL
local activeThreads = {}

local function stopAllThreads()
    for _, ctrl in pairs(activeThreads) do
        ctrl.stopFlag = true
    end
    task.wait(0.05)
    activeThreads = {}
end

local function startEggThread(plotId, slot, delay, qty)
    qty = qty or 1
    local ctrl = { stopFlag = false }
    activeThreads[slot] = ctrl
    task.spawn(function()
        if PRINT_VERBOSE then
            print(
                ('▶️ Start egg thread: slot=%d qty=%d delay=%.2fs'):format(
                    slot,
                    qty,
                    delay
                )
            )
        end
        while not ctrl.stopFlag do
            purchaseEgg(plotId, slot, qty)
            local t = 0
            while t < delay and not ctrl.stopFlag do
                task.wait(0.05)
                t = t + 0.05
            end
        end
        if PRINT_VERBOSE then
            print(('🛑 Dừng thread trứng #%d'):format(slot))
        end
    end)
end

-- LOGIC: quyết theo income (amount) + số Coins hiện có
-- Ngưỡng Coins (vn là số nguyên): 1m, 5m, 20m, 50m, 100m
local COIN_THRESHOLDS = {
    T1 = 1 * 10 ^ 6, -- 1,000,000
    T2 = 5 * 10 ^ 6, -- 5,000,000
    T3 = 20 * 10 ^ 6, -- 20,000,000
    T4 = 50 * 10 ^ 6, -- 50,000,000
    T5 = 100 * 10 ^ 6, --100,000,000
}

-- Quy tắc mapping dựa trên amount (/s):
-- amount < 1000  -> Tier 1
-- amount > 3000  -> Tier 2
-- amount > 8000  -> Tier 3
-- amount > 15000 -> Tier 4
-- else -> Tier 5 (lớn nhất)
-- Nhưng trước khi unlock house X, kiểm tra coins >= threshold tương ứng
-- Ngoài ra, set số trứng mua cho mỗi house theo yêu cầu:
-- Tier1: mua house1 nếu coins>1m; house1 mua x2 (tiết kiệm)
-- Tier2: mua house2 nếu coins>5m; house1 mua x2
-- Tier3: mua house3 nếu coins>20m; houses 1-3 mua x3
-- Tier4: mua house4 if coins>50m; houses1-3 x3; house4 x1
-- Tier5: coins>100m; houses1-3 x3; house4 x2; house5 x1

local function decideTierAndActions(amountPerSec, coins)
    -- amountPerSec có thể nil -> treat as 0
    amountPerSec = amountPerSec or 0
    coins = coins or 0

    local desiredTier
    if amountPerSec < 1000 then
        desiredTier = 1
    elseif amountPerSec > 15000 then
        desiredTier = 4 -- note: we'll check coins and may elevate to 5 later
    elseif amountPerSec > 8000 then
        desiredTier = 3
    elseif amountPerSec > 3000 then
        desiredTier = 2
    else
        desiredTier = 1
    end

    -- Build actions: housesToUnlock list, and eggQtyPerSlot table
    local housesToUnlock = {} -- list of house ids to attempt unlocking (1..n)
    local eggQtyPerSlot = {} -- eggQtyPerSlot[slot] = qty to buy each loop

    -- Helper to add houses 1..n
    local function addHousesRange(n)
        for i = 1, n do
            table.insert(housesToUnlock, i)
        end
    end

    if desiredTier == 1 then
        -- Tier1: if coins >= 1m -> unlock house1 and buy eggs x2 (slot1)
        if coins >= COIN_THRESHOLDS.T1 then
            addHousesRange(1)
            eggQtyPerSlot[1] = 2
        else
            -- not enough money: do nothing (no unlock), but we may still buy 0 or keep threads stopped
            eggQtyPerSlot[1] = 2 -- if you still want to buy eggs without unlocking, keep setting; unlocking will be skipped
        end
    elseif desiredTier == 2 then
        -- Tier2: need coins >=5m to unlock house2; house1 buy x2
        if coins >= COIN_THRESHOLDS.T2 then
            addHousesRange(2)
        else
            -- only unlock house1 if have >=1m
            if coins >= COIN_THRESHOLDS.T1 then
                addHousesRange(1)
            end
        end
        eggQtyPerSlot[1] = 3
        eggQtyPerSlot[2] = 2
    elseif desiredTier == 3 then
        -- Tier3: need coins >=20m to unlock house3; houses1-3 buy x3
        if coins >= COIN_THRESHOLDS.T3 then
            addHousesRange(3)
            eggQtyPerSlot[1] = 3
            eggQtyPerSlot[2] = 3
        else
            -- fallback: maybe unlock fewer houses according to lower thresholds
            if coins >= COIN_THRESHOLDS.T2 then
                addHousesRange(2)
                eggQtyPerSlot[1] = 3
                eggQtyPerSlot[2] = 2
            elseif coins >= COIN_THRESHOLDS.T1 then
                addHousesRange(1)
                eggQtyPerSlot[1] = 2
            end
        end
    elseif desiredTier == 4 then
        -- Tier4: need coins >=50m to unlock house4; houses1-3 x3, house4 x1
        if coins >= COIN_THRESHOLDS.T4 then
            addHousesRange(4)
            eggQtyPerSlot[1] = 3
            eggQtyPerSlot[2] = 3
            eggQtyPerSlot[3] = 1
            eggQtyPerSlot[4] = 1
        else
            -- fallback to tier3/2 based on coins
            if coins >= COIN_THRESHOLDS.T3 then
                addHousesRange(3)
                eggQtyPerSlot[1] = 3
                eggQtyPerSlot[2] = 3
                eggQtyPerSlot[3] = 2
            elseif coins >= COIN_THRESHOLDS.T2 then
                addHousesRange(2)
                eggQtyPerSlot[1] = 3
                eggQtyPerSlot[2] = 3
            elseif coins >= COIN_THRESHOLDS.T1 then
                addHousesRange(1)
                eggQtyPerSlot[1] = 2
            end
        end
    else
        -- desiredTier fallback (shouldn't reach)
        if coins >= COIN_THRESHOLDS.T5 then
            addHousesRange(5)
            eggQtyPerSlot[1] = 3
            eggQtyPerSlot[2] = 3
            eggQtyPerSlot[3] = 1
            eggQtyPerSlot[5] = 1
        end
    end

    -- Special: if coins >= 100m then full top-tier actions regardless of amountPerSec
    if coins >= COIN_THRESHOLDS.T5 then
        housesToUnlock = {}
        addHousesRange(5)
        eggQtyPerSlot[1] = 3
        eggQtyPerSlot[2] = 3
        eggQtyPerSlot[3] = 1
        eggQtyPerSlot[5] = 1
    end

    -- cap houses/slots by MAX_EGG_SLOT
    local finalHouses = {}
    for _, h in ipairs(housesToUnlock) do
        if #finalHouses < MAX_EGG_SLOT then
            table.insert(finalHouses, h)
        end
    end

    local finalEggQty = {}
    for i = 1, MAX_EGG_SLOT do
        finalEggQty[i] = eggQtyPerSlot[i] or 0
    end

    return finalHouses, finalEggQty
end

-- MAIN LOOP
task.spawn(function()
    local currentUnlocked = {} -- track which houses we've asked to unlock
    while true do
        local plot, amount = findMyPlotAndAmount()
        if not plot then
            if PRINT_VERBOSE then
                print('⏳ Chưa tìm thấy plot, chờ 10s...')
            end
            task.wait(10)
        else
            local plotId = tonumber(plot:GetAttribute('ID'))
                or tonumber(plot.Name)
                or 1
            local coins = getCurrentCoins()
            if PRINT_VERBOSE then
                print(
                    ('💰 Coins hiện có: %d | amount/s: %s'):format(
                        coins,
                        tostring(amount)
                    )
                )
            end

            -- quyết định action
            local housesToUnlock, eggQtyPerSlot =
                decideTierAndActions(amount, coins)

            -- if change in houses or eggQty -> restart threads
            local needRestart = false
            -- build simple comparator
            local function arrEqual(a, b)
                if #a ~= #b then
                    return false
                end
                for i = 1, #a do
                    if a[i] ~= b[i] then
                        return false
                    end
                end
                return true
            end
            if not arrEqual(currentUnlocked, housesToUnlock) then
                needRestart = true
            else
                -- also check qty differences with activeThreads keys
                for slot, ctrl in pairs(activeThreads) do
                    local expectedQty = eggQtyPerSlot[slot] or 0
                    -- if thread exists but expectedQty==0 -> restart (stop)
                    if expectedQty == 0 then
                        needRestart = true
                        break
                    end
                end
            end

            if needRestart then
                if PRINT_VERBOSE then
                    print(
                        '⚙️ Cập nhật cấu hình mua trứng/houses... restarting threads'
                    )
                end
                stopAllThreads()

                -- unlock houses decided (only if coins threshold satisfied inside unlockHouse decision previously)
                for _, h in ipairs(housesToUnlock) do
                    -- check coin threshold before attempting unlock (safety)
                    local okToUnlock = false
                    if h == 1 and coins >= COIN_THRESHOLDS.T1 then
                        okToUnlock = true
                    end
                    if h == 2 and coins >= COIN_THRESHOLDS.T2 then
                        okToUnlock = true
                    end
                    if h == 3 and coins >= COIN_THRESHOLDS.T3 then
                        okToUnlock = true
                    end
                    if h == 4 and coins >= COIN_THRESHOLDS.T4 then
                        okToUnlock = true
                    end
                    if h == 5 and coins >= COIN_THRESHOLDS.T5 then
                        okToUnlock = true
                    end

                    if okToUnlock then
                        unlockHouse(plotId, h)
                        task.wait(0.1)
                    else
                        if PRINT_VERBOSE then
                            print(
                                ('ℹ️ Bỏ qua unlock house #%d vì không đủ tiền (coins=%d)'):format(
                                    h,
                                    coins
                                )
                            )
                        end
                    end
                end

                -- start egg threads for 1..MAX_EGG_SLOT using eggQtyPerSlot (skip qty==0)
                for slot = 1, MAX_EGG_SLOT do
                    local qty = eggQtyPerSlot[slot] or 0
                    if qty and qty > 0 then
                        startEggThread(plotId, slot, EGG_DELAY, qty)
                        task.wait(0.05)
                    end
                end

                -- update currentUnlocked snapshot
                currentUnlocked = {}
                for _, h in ipairs(housesToUnlock) do
                    table.insert(currentUnlocked, h)
                end
            end

            task.wait(SIGN_RECHECK_INTERVAL)
        end
    end
end)

-- ===============================
-- 🧩 FULL AUTO PET & EGG MANAGER (1-8 pets, 9-10 eggs)
-- ===============================
local PET_SLOTS = { 1, 2, 3, 4, 5, 6, 7, 8 }
local EGG_SLOTS = { 9, 10 }
local UPDATE_INTERVAL = 0.25

local Rep = game:GetService('ReplicatedStorage')
local Players = game:GetService('Players')
local player = Players.LocalPlayer
local Network = Rep.Network

local Save = require(Rep.Library.Client.Save)
local HPillarItems = require(Rep.Library.Directory.HPillarItems)
local EggHalloween = require(Rep.Library.Directory.EggHalloween)
local Rarity = require(Rep.Library.Directory.Rarity)

------------------------------------------------------------
-- 🔢 Chuyển đổi text → số
------------------------------------------------------------
local function parseRate(text)
    if not text then
        return 0
    end
    text = text:gsub(',', ''):lower()
    local num, suffix = text:match('([%d%.]+)%s*([kmbtq]*)')
    num = tonumber(num)
    if not num then
        return 0
    end
    local mult = { k = 1e3, m = 1e6, b = 1e9, t = 1e12, q = 1e15 }
    return num * (mult[suffix] or 1)
end

------------------------------------------------------------
-- 🔹 Lấy CandyMultiplier
------------------------------------------------------------
local function getCandyMultiplier()
    local upgrades = (Save.Get().EventUpgrades or {})
    local level = upgrades.HalloweenCandyMultiplier or 0
    return 1 + 0.1 * level
end
local CandyMultiplier = getCandyMultiplier()

------------------------------------------------------------
-- 🐾 Lấy toàn bộ pet trong inventory + tính power thật
------------------------------------------------------------
local function getInventoryPets()
    local data = Save.Get()
    local inv = data and data.Inventory and data.Inventory.HPillar
    local pets, mapByUid = {}, {}
    if inv then
        for uid, info in pairs(inv) do
            local id = info.id
            local base = HPillarItems[id]
            if base then
                local perSec = base.BaseMoneyPerSecond or 0
                local tier = info.pt or 0
                local shiny = info.sh or (info._uq and info._uq.sh)
                local tierMultiplier = { [0] = 1, [1] = 2, [2] = 4, [3] = 6 }
                local shinyMultiplier, sunnyShinyMultiplier = 2, 8
                perSec = perSec * (tierMultiplier[tier] or 1)
                if tier == 3 and shiny then
                    perSec = perSec * sunnyShinyMultiplier / tierMultiplier[3]
                elseif shiny then
                    perSec = perSec * shinyMultiplier
                end
                perSec = perSec * CandyMultiplier
                local amount = info._am or 1
                local entry = {
                    uid = uid,
                    id = id,
                    name = base.DisplayName or id,
                    power = perSec,
                    amount = amount,
                    tier = tier,
                    shiny = shiny,
                }
                table.insert(pets, entry)
                mapByUid[uid] = entry
            end
        end
    end
    table.sort(pets, function(a, b)
        return a.power > b.power
    end)
    return pets, mapByUid
end

------------------------------------------------------------
-- 📍 Xác định plot của người chơi
------------------------------------------------------------
local function getPlayerPlot()
    for _, plot in pairs(workspace.__THINGS.Plots:GetChildren()) do
        local sign = plot:FindFirstChild('Build')
            and plot.Build:FindFirstChild('Sign')
        if sign then
            for _, gui in pairs(sign:GetDescendants()) do
                if gui:IsA('TextLabel') and gui.Text:find(player.Name) then
                    return plot
                end
            end
        end
    end
    return nil
end

------------------------------------------------------------
-- 🧱 Lấy danh sách pet đang đặt
------------------------------------------------------------
local function getPlacedPets(plot)
    local placed = {}
    local pillars =
        plot:WaitForChild('Interactable'):WaitForChild('Pillars'):GetChildren()
    for i, pillar in ipairs(pillars) do
        local base = pillar:FindFirstChild('Base')
        if base then
            local foundModel
            for _, model in pairs(workspace.__DEBRIS:GetChildren()) do
                local part = model:FindFirstChildWhichIsA('BasePart', true)
                if part and (part.Position - base.Position).Magnitude < 3 then
                    foundModel = model
                    break
                end
            end
            if foundModel then
                local uid = foundModel.Name
                local rate
                local petModel = foundModel:FindFirstChild('HalloweenPet')
                    or foundModel
                for _, obj in pairs(petModel:GetDescendants()) do
                    if
                        obj:IsA('TextLabel')
                        and (
                            obj.Text:find('/s')
                            or obj.Name:lower():find('rate')
                        )
                    then
                        rate = parseRate(
                            obj.Text:match('([%d%a%.]+)%s*/%s*s') or obj.Text
                        )
                        break
                    end
                end
                table.insert(
                    placed,
                    { slot = i, model = foundModel, uid = uid, power = rate }
                )
            end
        end
    end
    return placed
end

------------------------------------------------------------
-- 🔍 Tìm pet yếu nhất đang đặt
------------------------------------------------------------
local function findWeakestPlaced(placed, invMap)
    local weakest, minPower = nil, math.huge
    for _, p in ipairs(placed) do
        local invEntry = invMap[p.uid]
        local power = invEntry and invEntry.power or p.power or 0
        if power < minPower then
            minPower = power
            weakest = { slot = p.slot, uid = p.uid, power = power }
        end
    end
    return weakest, minPower
end

------------------------------------------------------------
-- 🐣 Gỡ pet ở ô trứng khi trứng nở
------------------------------------------------------------
local function handleHatchedEggs(plot)
    local placed = getPlacedPets(plot)
    for _, p in ipairs(placed) do
        for _, eggSlot in ipairs(EGG_SLOTS) do
            if p.slot == eggSlot then
                local hasEggText = false
                for _, obj in pairs(p.model:GetDescendants()) do
                    if
                        obj:IsA('TextLabel') and obj.Text:lower():find('egg')
                    then
                        hasEggText = true
                        break
                    end
                end
                if not hasEggText then
                    print(
                        '🐣 Egg hatched at slot',
                        eggSlot,
                        '→ removing old pet...'
                    )
                    pcall(function()
                        Network.HalloweenWorld_PickUp:InvokeServer(eggSlot)
                    end)
                    task.wait()
                end
            end
        end
    end
end

------------------------------------------------------------
-- 🥚 Đặt trứng theo ưu tiên hiếm + random
------------------------------------------------------------
local EGG_PRIORITY = {
    Mythical = { 'Coffin Egg' },
    Legendary = { 'Reaper Egg', 'Spider Egg' },
    Rare = { 'Bat Egg', 'Grave Egg' },
    Epic = { 'Ghost Egg', 'Cauldron Egg' },
    Basic = { 'Pumpkin Egg' },
}
local EGG_ORDER = { 'Mythical', 'Legendary', 'Rare', 'Epic', 'Basic' }

local function autoPlaceEggs()
    local data = Save.Get()
    local invEggs = data and data.Inventory and data.Inventory.EggHalloween
    if not invEggs then
        return
    end

    local eggs = {}
    for _, eggInfo in pairs(invEggs) do
        eggs[eggInfo.id] = eggInfo._am or 1
    end
    if next(eggs) == nil then
        return
    end

    local plot = getPlayerPlot()
    if not plot then
        return
    end

    local placed = getPlacedPets(plot)
    local occupied = {}
    for _, p in ipairs(placed) do
        occupied[p.slot] = true
    end

    for _, slot in ipairs(EGG_SLOTS) do
        if not occupied[slot] then
            local pick
            for _, rarity in ipairs(EGG_ORDER) do
                for _, eid in ipairs(EGG_PRIORITY[rarity] or {}) do
                    if eggs[eid] and eggs[eid] > 0 then
                        pick = eid
                        eggs[eid] = eggs[eid] - 1
                        break
                    end
                end
                if pick then
                    break
                end
            end
            if not pick then
                local remaining = {}
                for rarityName, list in pairs(EGG_PRIORITY) do
                    if
                        rarityName ~= 'Mythical'
                        and rarityName ~= 'Legendary'
                    then
                        for _, eid in ipairs(list) do
                            local amt = eggs[eid] or 0
                            for i = 1, amt do
                                table.insert(remaining, eid)
                            end
                        end
                    end
                end
                if #remaining > 0 then
                    pick = remaining[math.random(1, #remaining)]
                    eggs[pick] = eggs[pick] - 1
                end
            end
            if pick then
                print('🥚 Placing egg:', pick, '→ slot', slot)
                pcall(function()
                    Network.HalloweenWorld_PlaceEgg:InvokeServer(slot, pick)
                end)
                task.wait(0.1)
            end
        end
    end
end

------------------------------------------------------------
-- 🐾 Auto đặt hoặc thay pet mạnh nhất
------------------------------------------------------------
local function autoEquipPets()
    local plot = getPlayerPlot()
    if not plot then
        return
    end
    local invPets, invMap = getInventoryPets()
    local placed = getPlacedPets(plot)
    local placedBySlot = {}
    for _, p in ipairs(placed) do
        placedBySlot[p.slot] = p
    end

    -- Đặt pet mạnh nhất vào slot trống (1-8)
    for _, slot in ipairs(PET_SLOTS) do
        if not placedBySlot[slot] then
            local best = invPets[1]
            if best then
                print('✨ Placing pet:', best.name, '→ slot', slot)
                pcall(function()
                    Network.HalloweenWorld_PlacePet:InvokeServer(slot, best.uid)
                end)
                task.wait()
                return
            end
        end
    end

    -- Thay pet yếu bằng pet mạnh hơn
    local weakest, weakPower = findWeakestPlaced(placed, invMap)
    local best = invPets[1]
    if weakest and best and best.power > weakPower then
        print(
            string.format(
                '🔁 Replacing slot %d (%.2f/s) → %s (%.2f/s)',
                weakest.slot,
                weakPower,
                best.name,
                best.power
            )
        )
        pcall(function()
            Network.HalloweenWorld_PickUp:InvokeServer(weakest.slot)
        end)
        task.wait(0.5)
        pcall(function()
            Network.HalloweenWorld_PlacePet:InvokeServer(weakest.slot, best.uid)
        end)
        task.wait()
    end
end

------------------------------------------------------------
-- 🔁 Vòng lặp chính
------------------------------------------------------------
task.spawn(function()
    while task.wait(UPDATE_INTERVAL) do
        local ok, err = pcall(function()
            local plot = getPlayerPlot()
            if not plot then
                return
            end
            handleHatchedEggs(plot)
            autoPlaceEggs()
            autoEquipPets()
        end)
        if not ok then
            warn('⚠️ Loop error:', err)
        end
    end
end)

------------------------------------------------------------
-- 🎁 Tự động claim toàn bộ 10 slot
------------------------------------------------------------
task.spawn(function()
    while task.wait(5) do
        print('🎁 Claim toàn bộ 10 slot...')
        for i = 1, 10 do
            task.spawn(function()
                local success, err = pcall(function()
                    Network.HalloweenWorld_Claim:InvokeServer(i)
                end)
                if success then
                    print('✅ Claim slot', i, 'thành công!')
                else
                    warn('⚠️ Claim slot', i, 'lỗi:', err)
                end
            end)
        end
        print('🎉 Hoàn tất claim toàn bộ 10 slot!')
    end
end)
-- 🎃 AUTO HALLOWEEN UPGRADE PRIORITY SYSTEM
-- by ChatGPT (GPT-5)
-- Ưu tiên: Diamonds > WitchHats > Candy > Random các loại khác

local Rep = game:GetService('ReplicatedStorage')
local Network = Rep:WaitForChild('Network')
local Save = require(Rep.Library.Client.Save)

local PURCHASE = Network:FindFirstChild('EventUpgrades: Purchase')

-- 🎯 Danh sách nâng cấp có trong game
local AllUpgrades = {
    'HalloweenCandyMultiplier',
    'HalloweenTrickOrTreatLuck',
    'HalloweenHugeLuck',
    'HalloweenTitanicLuck',
    'HalloweenMoreDiamonds',
    'HalloweenMoreWitchHats',
    'HalloweenEggLuck',
}

-- ⚙️ Ưu tiên nâng trước
local Priority = {
    'HalloweenMoreDiamonds',
    'HalloweenMoreWitchHats',
    'HalloweenCandyMultiplier',
}

-- 🕐 Thời gian delay giữa mỗi lần nâng (để tránh spam)
local DELAY = 1.5

-- 🔁 Hàm lấy cấp độ hiện tại
local function getUpgradeLevel(name)
    local profile = Save.Get()
    local upgrades = profile.EventUpgrades or {}
    return upgrades[name] or 0
end

-- 📈 Hàm nâng cấp cụ thể
local function upgrade(name)
    local current = getUpgradeLevel(name)
    if current >= 10 then
        return false -- đạt giới hạn
    end
    local result = PURCHASE:InvokeServer(name)
    if result == true or (type(result) == 'table' and result.success) then
        print('✅ Nâng cấp thành công:', name, '→ cấp:', current + 1)
    else
        print('❌ Không thể nâng:', name, '| Kết quả:', result)
    end
    task.wait(DELAY)
    return true
end

-- 🔁 Vòng lặp chính
while task.wait(DELAY) do
    local doneAllPriority = true

    -- 1️⃣ Nâng ưu tiên
    for _, name in ipairs(Priority) do
        local level = getUpgradeLevel(name)
        if level < 10 then
            doneAllPriority = false
            print('⚙️ Đang nâng:', name, '(hiện tại:', level .. ')')
            upgrade(name)
        end
    end

    -- 2️⃣ Nếu tất cả ưu tiên đã max thì nâng ngẫu nhiên phần còn lại
    if doneAllPriority then
        local others = {}
        for _, name in ipairs(AllUpgrades) do
            if
                not table.find(Priority, name)
                and getUpgradeLevel(name) < 10
            then
                table.insert(others, name)
            end
        end

        if #others > 0 then
            local pick = others[math.random(1, #others)]
            print('🎲 Random nâng:', pick)
            upgrade(pick)
        else
            print(
                '🏁 Tất cả nâng cấp đã đạt cấp tối đa (10)'
            )
            break
        end
    end
end
-- 🌿 CLEAN WORLD & KEEP LOCAL PLAYER ONLY
-- by ChatGPT (optimized)

local Players = game:GetService('Players')
local player = Players.LocalPlayer

-- ⚙️ Cấu hình: Bật/Tắt tính năng xoá player khác
local KEEP_ONLY_LOCALPLAYER = true

-- 🧍‍♂️ Hàm xoá tất cả player khác
local function removeOtherPlayers()
    if not KEEP_ONLY_LOCALPLAYER then
        return
    end
    for _, plr in ipairs(Players:GetChildren()) do
        if plr ~= player then
            pcall(function()
                plr:Destroy()
                warn('🧹 Đã xoá player:', plr.Name)
            end)
        end
    end
end

-- 🚫 Khi có player mới join -> xoá ngay và quét lại toàn bộ
Players.PlayerAdded:Connect(function(plr)
    if not KEEP_ONLY_LOCALPLAYER then
        return
    end
    if plr ~= player then
        pcall(function()
            plr:Destroy()
            warn('🚫 Player mới bị xoá:', plr.Name)
        end)
    end
    -- kiểm tra lại toàn bộ danh sách
    removeOtherPlayers()
end)

-- 🧹 Khi có player rời -> đảm bảo danh sách sạch
Players.PlayerRemoving:Connect(function(_)
    task.defer(removeOtherPlayers)
end)

-- ⏳ Vòng kiểm tra liên tục để đảm bảo không lọt player ẩn
task.spawn(function()
    while task.wait(3) do
        removeOtherPlayers()
    end
end)

-- Chạy lần đầu tiên
removeOtherPlayers()

-- 📝 Danh sách đường dẫn thủ công muốn xoá
local ManualPathsToDelete = {
    'workspace.GardenCoinShop',
    'workspace.Debris',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.PotionShelf',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.WitchesBrewLeaderboard',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.WitchesBrewPlate',
    'workspace.Interaction.UpdateItems.WitchesBrewEvent.Witch.Broom',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.Casket',
    "workspace.Interaction.UpdateItems.HalloweenMarketEvent['Devious Pumpkin']",
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenEloise',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenLights',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenPlate',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.HalloweenSteven',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.Model1',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.Model2',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.PumpkinLight',
    'workspace.Interaction.UpdateItems.HalloweenMarketEvent.CandyCornSpecialCurrencyUIOverlap',
    'workspace.HalloweenOuter',
    'workspace.FlyBorder',
    'workspace.BlockPartyOuter',
}

-- 🌟 Ẩn phần hiển thị của Part nhưng giữ nguyên Prompt + thư mục con
local function hidePartVisualButKeepContents(part)
    part.Transparency = 1
    part.CastShadow = false
    part.CanCollide = false
    part.CanTouch = false
    part.CanQuery = false

    for _, child in ipairs(part:GetChildren()) do
        if
            child:IsA('SpecialMesh')
            or child:IsA('Decal')
            or child:IsA('MeshPart')
            or child:IsA('UnionOperation')
        then
            child:Destroy()
        end
    end
end

-- 🧹 Xoá phần hiển thị folder nhưng giữ Prompt
local function clearFolderVisualButKeepPrompt(folder)
    for _, desc in ipairs(folder:GetDescendants()) do
        if desc:IsA('BasePart') then
            local prompt = desc:FindFirstChildWhichIsA('ProximityPrompt')
            if prompt then
                hidePartVisualButKeepContents(desc)
            else
                desc:Destroy()
            end
        elseif
            desc:IsA('MeshPart')
            or desc:IsA('UnionOperation')
            or desc:IsA('Decal')
        then
            desc:Destroy()
        end
    end
end

-- 🌳 Xoá cây & quả
local function clearPlantAndFruits(plant)
    clearFolderVisualButKeepPrompt(plant)
    local fruits = plant:FindFirstChild('Fruits')
    if fruits then
        for _, fruit in ipairs(fruits:GetChildren()) do
            clearFolderVisualButKeepPrompt(fruit)
        end
    end
end

-- 🧼 Xoá đường dẫn thủ công
local function deleteManualPaths()
    for _, path in ipairs(ManualPathsToDelete) do
        local success, target = pcall(function()
            return loadstring('return ' .. path)()
        end)
        if success and target then
            warn('Xóa theo đường dẫn:', path)
            target:Destroy()
        end
    end
end

-- 🧹 Xoá plot người chơi khác
local function deleteOtherPlayerPlots()
    local plots = workspace:FindFirstChild('__THINGS')
        and workspace.__THINGS:FindFirstChild('Plots')
    if plots then
        for _, plot in ipairs(plots:GetChildren()) do
            local sign = plot:FindFirstChild('Build')
                and plot.Build:FindFirstChild('Sign')
            local isMine = false
            if sign then
                for _, gui in ipairs(sign:GetDescendants()) do
                    if gui:IsA('TextLabel') and gui.Text:find(player.Name) then
                        isMine = true
                        break
                    end
                end
            end
            if not isMine then
                plot:Destroy()
            end
        end
    end
end

-- 🧼 Vòng lặp dọn map cũ liên tục
task.spawn(function()
    while task.wait(2) do
        pcall(function()
            -- ❌ Xoá Lighting object
            for _, obj in ipairs(game:GetService('Lighting'):GetChildren()) do
                obj:Destroy()
            end

            -- ❌ Xoá Debris
            if workspace:FindFirstChild('Debris') then
                for _, obj in ipairs(workspace.Debris:GetChildren()) do
                    obj:Destroy()
                end
            end

            -- ❌ Xoá Terrain (giữ object Terrain nhưng xoá tất cả nội dung)
            if workspace:FindFirstChild('Terrain') then
                workspace.Terrain:Clear()
            end

            -- 🧼 Xoá theo đường dẫn thủ công
            deleteManualPaths()

            -- 🌾 Dọn toàn bộ Farm
            for _, farm in workspace:GetChildren() do
                if farm.Name == 'Farm' then
                    for _, subFarm in ipairs(farm:GetChildren()) do
                        if
                            subFarm.Name == 'Farm'
                            and subFarm:FindFirstChild('Important')
                            and subFarm.Important:FindFirstChild('Data')
                            and subFarm.Important.Data:FindFirstChild('Owner')
                        then
                            local isMine = (
                                subFarm.Important.Data.Owner.Value
                                == player.Name
                            )
                            if not isMine then
                                subFarm:Destroy()
                            else
                                local important = subFarm.Important
                                if
                                    important:FindFirstChild('Plants_Physical')
                                then
                                    for _, plant in
                                        ipairs(
                                            important.Plants_Physical:GetChildren()
                                        )
                                    do
                                        clearPlantAndFruits(plant)
                                    end
                                end
                                for _, folderName in ipairs({
                                    'Decorations',
                                    'Fences',
                                    'Cosmetics',
                                }) do
                                    if important:FindFirstChild(folderName) then
                                        for _, obj in
                                            ipairs(
                                                important[folderName]:GetChildren()
                                            )
                                        do
                                            obj:Destroy()
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end

            -- 🧹 Xoá plot người chơi khác
            deleteOtherPlayerPlots()
            -- 🧹 Xoá player khác (đảm bảo kiểm tra định kỳ)
            removeOtherPlayers()
        end)
    end
end)

print(
    '✅ Script hoàn thiện: Dọn map & chỉ giữ lại LocalPlayer 🌿✨'
)
