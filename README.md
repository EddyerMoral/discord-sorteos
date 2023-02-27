# 🎉 DISCORD-SORTEOS 🎉

Sistema avanzado de sorteos de discord, mediante mongodb con compatibilidad de SlashCommands y PrefixCommands.

Tendremos actualizaciónes en la que se añadirán nuevos métodos de comandos.

# Descargar

```cli
npm i discord-sorteos
```


# Actualizaciones:

### Update 1.2.6
- Actualización Estable.
- Dos metodos añadidos: edit y deleteAll.
- Errores Solucionados.
- Ejemplos de comandos actualizados.
- Código interno de los sorteos actualizado.



# Errores, fallas y problemas
- Si encuentra algún problema, no dude en abrir un problema en nuestro <a href="https://github.com/EddyerMoral/discord-sorteos/issues">GitHub</a> o unirse a [Discord Server](https://discord.gg/PKJA9yFw6Y).



# Ejemplo

**_<p style="text-align: center;">[![Ejemplo](https://cdn.discordapp.com/attachments/1067640383141589083/1079561377812856883/Screenshot_20230226-181831.png)](https://discord.gg/PKJA9yFw6Y)</p>_**

# Configurando

### Parámetros del cliente

```js
const { Client, GatewayIntentBits } = require("discord.js");
const { SorteoManager } = require("discord-sorteos");

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.GuildMembers,
    GatewayIntentBits.MessageContent,
  ],
});

const Sorteos = new SorteoManager(client, {
	Embed: {
		Color: '#FFFFFF',
		ColorEnd: '#000000',
		// Title: '', // Usar el por defecto 
		// TitleEnd: '', // Usar el por defecto 
		// Image: '', // Usar el por defecto 
		// ImageEnd: '', // Usar el por defecto 
		EmojiPrize: '💎',
		EmojiTime: '⏳',
		EmojiWinners: '👥',
		EmojiHostedBy: '👤',
	},
	Boton: {
		Texto: 'Entrar',
		Emoji: '🎉',
	}
});
client.sorteos = Sorteos;

client.on("ready", () => {
  console.log(`[Studios] = Bot iniciado.`);
});
```

### Comandos

```js
client.on("interactionCreate", async (interaction) => {
	await interaction.deferReply({ ephemeral: true }).catch((e) => {});
	if (interaction.isChatInputCommand()) {
		const cmdName = interaction.commandName;
		switch (cmdName) {
			// Ejemplo de Comenzar Sorteó 
			case 'start':
			const ms = require('ms');
			const channel = interaction.options.getChannel('canal');
	    	const premio = interaction.options.getString('premio');
    		const ganadores = interaction.options.getNumber('ganadores');
    		const duracion = interaction.options.getString('duracion');
    		const convertTime = ms(duracion);
    		const Start = await client.sorteos.start(channel, interaction, {
    		    premio: premio,
    		    ganadores: ganadores,
    		    duracion: convertTime
    		}).catch((e) => {});
    		if(Start) {
    		    return interaction.reply({
    			embeds: [
    				new EmbedBuilder()
    				.setColor(client.color)
    				.setDescription(`${client.sorteo} El sorteo ha sido creado correctamente.`)
    				.addFields([{
    					name: `${client.channel} Canal:`,
    					value: `${channel}`,
    					inline: true
    				},{
    					name: `<:Prize:1078549475137691780> **|** Premio:`,
    					value: `${premio}`,
    					inline: true
    				},{
    					name: `<:Winners:1078549421077311528> **|** Ganadores:`,
    					value: `${ganadores}`,
    					inline: true
    				},{
    					name: `${client.time} Tiempo:`,
    					value: `${convertTime}`,
    					inline: true
    				}])
    			]
    		});
    		} else if(!Start) {
    		    return interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El sorteo no ha iniciado, ah ocurrido un error.`)
    				]
    			});
    		}
			return;
			break;
			// Ejemplo de Terminar Sorteó 
			case 'end':
			const messageId = interaction.options.getString('id');
    		let Ended = await client.sorteos.end(messageId).catch((e) => {});
    		if(Ended) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El sorteo ha sido finalizado correctamente.`)
    				]
    			});
    		} else if(!Ended) {
    			return interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El id del sorteo es invalido o el sorteo ya no existe.`)
    				],
    				ephemeral: true
                });
    		}
			return;
			break;
			// Ejemplo de Reroll
			case 'reroll':
			const messageId = interaction.options.getString('id');
    		let Reroll = await client.sorteos.reroll(messageId).catch((e) => {});
    		if(Reroll) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El sorteo ha sido rerolleado correctamente.`)
    				],
    				ephemeral: true
    			});
    		} else if (!Reroll) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El id del sorteo es invalido o el sorteo ya no existe.`)
    				],
    				ephemeral: true
                })
    		}
			return;
			break;
			// Ejemplo de eliminar un Sorteó 
			case 'delete':
			const messageId = interaction.options.getString('id');
    		let Delete = await client.sorteos.delete(messageId).catch((e) => {});
    		if(Delete) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El sorteo ha sido eliminado correctamente.`)
    				]
    			});
    		} else if(!Delete) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El id del sorteo es invalido o el sorteo ya no existe.`)
    				],
    				ephemeral: true
    			});
    		}
			return;
			break;
			// Ejemplo de editar un Sorteó 
			case 'edit':
			const id = interaction.options.getString('id');
    		const premio = interaction.options.getString('premio');
    		const ganadores = interaction.options.getNumber('ganadores');
    		const Edit = await client.sorteos.edit(id, {
    			premio: premio,
    			ganadores: ganadores
    		}).catch((e) => {});
    		if(Edit) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El sorteo ha sido editado correctamente.`)
    					.addFields([{
    						name: `<:Prize:1078549475137691780> **|** Premio:`,
    						value: `${premio}`,
    						inline: true
    					},{
    						name: `<:Winners:1078549421077311528> **|** Ganadores:`,
    						value: `${ganadores}`,
    						inline: true
    					}])
    				]
    			});
    		} else if(!Edit) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El id del sorteo es invalido o el sorteo ya no existe.`)
    				],
    				ephemeral: true
                });
    		}
			return;
			break;
			// Ejemplo de eliminar todos los Sorteos 
			case 'deleteAll':
			let Delete = await client.sorteos.deleteAll(interaction.guild.id).catch((e) => {});
    		if(Delete) {
    			return interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} Todos los sorteos han sido eliminados correctamente.`)
    				]
    			});
    		} else if(!Delete) {
    			interaction.reply({
    				embeds: [
    					new EmbedBuilder()
    					.setColor(client.color)
    					.setDescription(`${client.sorteo} El este servidor no tiene sorteos iniciados ni finalizados.`)
    				],
    				ephemeral: true
                });
    		}
			return;
			break;
		}
	}
```

### Eventos del SorteoManager 


```js
const { EmbedBuilder } = require('discord.js');
const { stripIndent } = require('common-tags');

// Parámetro **member** puede ser usado para enviar un dm.
// Parámetro **sorteo** obteniendo los datos del schema para crear un mensaje.
// Parámetro **boton** usado para hacer un reply al usuario que se salió correctamente del sorteo.

client.sorteos.on('SorteoReady', (name) => {
	console.log('[Studios] = Sistema de sorteos encendido.')
});

client.sorteos.on('SorteoWinner', (message, sorteo) => {
	const Winners = sorteo.Winners
	.map((winner) => `<@${winner.userID}>`).join(" | ");
	message.channel?.send({
		content: `> Felicidades, ${Winners}! Ganaste: **${sorteo.Prize}**!`
	});
	sorteo.Winners.map(async (user) => {
		const u = await message.guild.members.fetch(user.userID);
		u.send({
			embeds: [
				new EmbedBuilder()
				.setColor(client.color)
				.setDescription(`> ${client.sorteo} Ganaste el sorteo: [[Link del Sorteo]](${message.url})`)
			]
		});
	});
});

client.sorteos.on('SorteoNoWinner', (message, sorteo) => {
	message.channel?.send({
		content: `> El sorteó ha sido cancelado, razón: \`No hay participantes válidos\` | <@${sorteo.HostedBy}>`
	});
});

client.sorteos.on('SorteoInvalid', (member, sorteo, boton) => {
	boton.reply({
		embeds: [
			new EmbedBuilder()
			.setColor(client.color)
			.setDescription(stripIndent`
			${client.sorteo} El sorteo ya a finalizado.`)
		],
		ephemeral: true
	});
});

client.sorteos.on('SorteoJoinUser', (member, sorteo, boton) => {
	boton.reply({
		embeds: [
			new EmbedBuilder()
			.setColor(client.color)
			.setDescription(stripIndent`
			${client.sorteo} Has entrado correctamente al sorteo: **${sorteo.Prize}**.`)
		],
		ephemeral: true
	});
});

client.sorteos.on('SorteoLeftUser', (member, sorteo, boton) => {
	boton.reply({
		embeds: [
			new EmbedBuilder()
			.setColor(client.color)
			.setDescription(stripIndent`
			${client.sorteo} Has salido correctamente del sorteo: **${sorteo.Prize}**.`)
		],
		ephemeral: true
	});
});

```
