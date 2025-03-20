local keys = {
  semanal = {
    key = "SEMANAL123",
    expira_em = function()
      local agora = os.time()
      local proxima_segunda = agora + (8 - (os.date("%w", agora) + 1)) % 7 * 24 * 3600
      return proxima_segunda
    end,
    hwid = nil
  },
  mensal = {
    key = "MENSAL456",
    expira_em = function()
      local agora = os.time()
      local primeiro_dia_proximo_mes = os.time { year = os.date("%Y", agora), month = os.date("%m", agora) + 1, day = 1, hour = 0, min = 0, sec = 0 }
      return primeiro_dia_proximo_mes
    end,
    hwid = nil
  },
  permanente = {
    key = "PERMANENTE789",
    expira_em = function()
      return math.huge
    end,
    hwid = nil
  }
}

local function get_hwid()
  return game:GetService("Os").ComputerId
end

local function validar_chave(tipo, chave_inserida)
  local hwid_atual = get_hwid()
  if keys[tipo] then
    if keys[tipo].key == chave_inserida then
      local agora = os.time()
      if agora < keys[tipo].expira_em() then
        if keys[tipo].hwid == nil or keys[tipo].hwid == hwid_atual then
          keys[tipo].hwid = hwid_atual
          return true, "Válido"
        else
          return false, "HWID Inválido"
        end
      else
        return false, "Expirado"
      end
    else
      return false, "Inválido"
    end
  else
    return false, "Tipo de chave inválido"
  end
end

local function abrir_menu()
  print("Menu aberto! Funções desbloqueadas.")
end

local function handle_key_input(tipo, chave_inserida)
  local valido, mensagem = validar_chave(tipo, chave_inserida)

  if valido then
    print("Chave " .. tipo .. " validada com sucesso.")
    abrir_menu()
  else
    print("Chave " .. tipo .. " inválida ou expirada: " .. mensagem)
  end
end

local function alterar_chave(tipo, nova_chave)
  if keys[tipo] then
    keys[tipo].key = nova_chave
    print("Chave " .. tipo .. " alterada para: " .. nova_chave)
  else
    print("Tipo de chave inválido: " .. tipo)
  end
end

local function resetar_hwid(tipo)
    if keys[tipo] then
        keys[tipo].hwid = nil
        print("HWID da chave " .. tipo .. " resetado.")
    else
        print("Tipo de chave inválido: " .. tipo)
    end
end

-- Exemplos de uso (precisa ser executado dentro do ambiente Roblox)
local hwid_atual = get_hwid()

print("HWID atual: " .. hwid_atual)

-- Testando a chave semanal (primeira ativação)
handle_key_input("semanal", "SEMANAL123")

-- Imprimindo o estado das chaves
print("\nEstado atual das chaves:")
for tipo, dados in pairs(keys) do
  print(tipo .. ": Key = " .. dados.key .. ", Expira em = " .. os.date("%Y-%m-%d %H:%M:%S", dados.expira_em()) .. ", HWID = " .. (dados.hwid or "Nenhum"))
end
