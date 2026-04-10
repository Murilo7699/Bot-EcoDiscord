import discord
from discord.ext import commands
import random
import requests

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix='/', intents=intents)

perguntas = {
    "Qual desses materiais demora mais para se decompor?\n(a) Papel\n(b) Plástico\n(c) Casca de banana": "b",
    "Qual atitude economiza mais água?\n(a) Banho demorado\n(b) Fechar a torneira ao escovar os dentes\n(c) Lavar calçada com mangueira": "b",
    "O que é melhor para o meio ambiente?\n(a) Sacola plástica\n(b) Sacola reutilizável\n(c) Sacola de papel sempre": "b",
    "Qual dessas opções gera menos lixo?\n(a) Garrafa descartável\n(b) Lata\n(c) Garrafa reutilizável": "c",
    "Qual é a melhor forma de economizar energia?\n(a) Deixar luz acesa\n(b) Apagar luz ao sair\n(c) Usar tudo ao mesmo tempo": "b",
    "Qual desses polui mais os oceanos?\n(a) Plástico\n(b) Papel\n(c) Restos de comida": "a",
    "O que é reciclagem?\n(a) Jogar lixo fora\n(b) Reutilizar materiais para fazer novos produtos\n(c) Queimar lixo": "b",
    "Qual dessas atitudes ajuda o meio ambiente?\n(a) Jogar lixo no chão\n(b) Separar lixo reciclável\n(c) Desperdiçar água": "b",
    "Qual é a melhor opção para transporte sustentável?\n(a) Carro sozinho\n(b) Bicicleta\n(c) Moto": "b",
    "Qual desses leva mais tempo para se decompor?\n(a) Vidro\n(b) Papel\n(c) Casca de fruta": "a"
}

@bot.event
async def on_message(message):
    if message.author.bot:
        return

    if message.channel.id != CANAL_PERMITIDO:
        return

    await bot.process_commands(message)

@bot.event
async def on_ready():
    print(f'Estamos logados como {bot.user}')

@bot.event
async def on_member_join(member):
    canal = member.guild.system_channel
    if canal:
        await canal.send(
            f'👋 Olá {member.mention}, eu sou o Zion 🌱\nDigite /ajuda para ver minhas funções!'
        )

@bot.event
async def on_message(message):
    if message.author.bot:
        return
    await bot.process_commands(message)

@bot.command()
async def ajuda(ctx):
    await ctx.send(
        "📜 *Comandos do bot:*\n"
        "/ajuda → Mostra os comandos\n"
        "/ecodicas → Recebe uma dica ecológica 🌱\n"
        "/ecoquiz → Responda uma pergunta sobre sustentabilidade 🌍\n"
        "/ecocuriosidade → Descubra curiosidades sobre sustentabilidade 🌿\n"
        "/ecoacao → Receba ações ecológicas para praticar hoje 🚀"
    )

@bot.command()
async def ecoquiz(ctx):
    pergunta, resposta = random.choice(list(perguntas.items()))

    await ctx.send(f"🌍 {ctx.author.mention}\n{pergunta}\nResponda com a, b ou c!")

    def check(m):
        return m.author == ctx.author and m.channel == ctx.channel

    try:
        msg = await bot.wait_for('message', timeout=15.0, check=check)

        if msg.content.lower() == resposta:
            await ctx.send("✅ Correto! Boa atitude ecológica!")
        else:
            await ctx.send(f"❌ Errado! A resposta certa era **{resposta.upper()}**.")
    except:
        await ctx.send("⏰ Tempo esgotado!")

@bot.command()
async def ecodicas(ctx):
    dicas = [
        "🌱 Dica: Separe corretamente o lixo reciclável!",
        "🌍 Dica: Evite usar plástico descartável sempre que possível!",
        "💡 Dica: Apague as luzes ao sair de um ambiente!",
        "🚿 Dica: Tome banhos mais curtos para economizar água!",
        "🛍️ Dica: Use sacolas reutilizáveis nas compras!",
        "🍎 Dica: Prefira alimentos locais e da estação!",
        "🚲 Dica: Caminhe ou use bicicleta em trajetos curtos!",
        "🔌 Dica: Tire aparelhos da tomada quando não estiver usando!",
        "📄 Dica: Evite desperdício de papel, use o digital quando puder!",
        "🥤 Dica: Use garrafas reutilizáveis!",
        "🍽️ Dica: Evite desperdiçar comida!",
        "🧼 Dica: Prefira produtos de limpeza menos poluentes!",
        "📦 Dica: Reutilize embalagens sempre que possível!",
        "🌞 Dica: Aproveite a luz natural durante o dia!",
        "🧴 Dica: Escolha produtos com menos embalagem!",
        "🌊 Dica: Nunca jogue lixo na rua ou em rios!",
        "🌿 Dica: Cuide de plantas — elas ajudam o meio ambiente!",
        "📚 Dica: Aprenda mais sobre sustentabilidade e compartilhe!",
        "⚡ Dica: Evite deixar carregadores na tomada sem uso!",
        "🧊 Dica: Não abra a geladeira sem necessidade para economizar energia!"
    ]

    await ctx.send(random.choice(dicas))

@bot.command()
async def ecoacao(ctx):
    acoes = [
        "🌱 Ação: Hoje, leve uma garrafa reutilizável ao sair de casa.",
        "🚿 Ação: Tente reduzir seu banho para no máximo 5 minutos hoje.",
        "🛍️ Ação: Use uma sacola reutilizável na próxima compra.",
        "💡 Ação: Apague todas as luzes que não estiver usando hoje.",
        "🍽️ Ação: Evite desperdiçar comida em pelo menos uma refeição hoje.",
        "🔌 Ação: Tire pelo menos 3 aparelhos da tomada antes de dormir.",
        "📦 Ação: Reutilize alguma embalagem hoje em vez de jogar fora.",
        "🚲 Ação: Faça um trajeto curto a pé ou de bicicleta hoje.",
        "🧴 Ação: Escolha um produto com menos embalagem na próxima compra.",
        "🌿 Ação: Cuide de uma planta ou plante algo hoje."
    ]

    selecionadas = random.sample(acoes, 3)

    mensagem = f"🌍 {ctx.author.mention}, aqui estão 3 ações ecológicas para hoje:\n\n"
    for acao in selecionadas:
        mensagem += f"{acao}\n"

    await ctx.send(mensagem)

@bot.command()
async def ecocuriosidade(ctx):
    curiosidades = [
        "🌍 Curiosidade: Carros elétricos nem sempre são 100% sustentáveis. Se a energia vem de fontes poluentes, o impacto continua.",
        "🔋 Curiosidade: O descarte incorreto de baterias de carros elétricos pode contaminar o solo e a água.",
        "♻️ Curiosidade: O plástico pode levar mais de 400 anos para se decompor.",
        "🌊 Curiosidade: Milhões de toneladas de plástico vão parar nos oceanos todos os anos.",
        "🌳 Curiosidade: Uma árvore pode absorver até 150 kg de CO₂ por ano.",
        "💡 Curiosidade: Lâmpadas LED usam até 80% menos energia.",
        "🚿 Curiosidade: Um banho de 10 minutos pode gastar até 90 litros de água.",
        "🍽️ Curiosidade: Cerca de 1/3 da comida do mundo é desperdiçada.",
        "🔌 Curiosidade: Aparelhos em standby continuam consumindo energia.",
        "🗑️ Curiosidade: Lixo eletrônico precisa de descarte especial."
    ]

    await ctx.send(random.choice(curiosidades))

bot.run("Segredo")
