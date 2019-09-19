<template>
	<v-container grid-list-xl>
		<v-layout row wrap fill-height>
			<v-flex xs12 sm7>
				<v-divider></v-divider>
				<v-subheader>Conversations</v-subheader>

				<v-list>
					<template v-for="(conversation, index) in conversations">
						<v-list-tile :key="conversation.auth_address + '_' + conversation.id" avatar :class="{ 'active': activeConversationIndex == index }" @click="showConversation(index)">
							<v-list-tile-content>
								<v-list-tile-title>{{ conversation.type == 'task_conversation' ? 'Task: ' : '' }}{{ conversation.title }}{{ conversation.type == 'task_assigned' ? ' (task assigned)' : '' }}</v-list-tile-title>
							</v-list-tile-content>
						</v-list-tile>
					</template>
				</v-list>
			</v-flex>
			<v-flex xs12 sm5>
				<v-tabs-items v-model="activeConversation">
					<v-tab-item key="0">
						<v-divider></v-divider>
						<v-subheader>Filter and Sort</v-subheader>
						<v-text-field prepend-icon="search" placeholder="Search" v-model="searchQuery"></v-text-field>
						<v-radio-group v-model="filter">
							<v-radio label="All" value="all"></v-radio>
							<v-radio label="Task Conversations" value="task_conversations"></v-radio>
							<v-radio label="Task-Assigned" value="task_assigned"></v-radio>
							<v-radio label="Task-Related (Task Conversations and Task-Assigned)" value="task_related"></v-radio>
						</v-radio-group>
					</v-tab-item>
					<v-tab-item v-for="(conversation, index) in conversations" :key="index + 1" lazy>
						<div v-if="activeConversationIndex != null">
							<v-divider></v-divider>
							<v-subheader>{{ conversation.type == 'task_conversation' ? 'Task: ' : '' }}{{ conversation.title }}</v-subheader>
							<div style="padding-left: 16px; padding-right: 16px;">
								<div v-html="getMarkdown(conversation.body)"></div>
								<component v-if="conversation.task" :is="task_popover_card" :user-settings="userSettings" :user-info="userInfo" :lang-translation="langTranslation" :task="conversation.task" :conversation-type="conversation.type" style="margin-bottom: 8px;"></component>

								<v-btn flat color="blue">Comment</v-btn>
								<span v-if="!conversation.task">
									<v-btn flat color="blue">Assign Task</v-btn>
								</span>
								<v-btn flat color="blue">Assign a File</v-btn>
							</div>

							<div v-if="conversation.files">
								<v-divider style="margin-top: 15px;"></v-divider>
								<v-subheader>Assigned Files</v-subheader>
								<v-list dense>
									<template v-for="file in conversation.files">
										<v-list-tile :key="file.auth_address + '_' + file.id" avatar @click="">
											<v-list-tile-content>
												<v-list-tile-title>{{ file.title }}</v-list-tile-title>
											</v-list-tile-content>
										</v-list-tile>
									</template>
								</v-list>
							</div>


							<div v-if="conversation.comments && conversation.comments.length > 0">
								<v-divider style="margin-top: 15px;"></v-divider>
								<v-subheader>Comments</v-subheader>
								<div v-for="comment in conversation.comments" :key="comment.auth_address + '_' + comment.id" style="margin-bottom: 15px; padding-left: 16px; padding-right: 16px;">
									<strong>{{ comment.cert_user_id }}</strong><br>
									<span v-html="getMarkdown(comment.body)"></span>
								</div>
							</div>
						</div>
					</v-tab-item>
				</v-tabs-items>
			</v-flex>
		</v-layout>
	</v-container>
</template>

<style lang="css" scoped>
    .active {
        background-color: #D3D3D3;
    }
    .theme--dark .active {
        background-color: #505050;
    }
    .done {
        background-color: #E3E3E3;
    }
    .theme--dark .done {
        background-color: #505050;
    }
	.markdown p {
		margin-bottom: 8px !important;
	}
</style>

<script>
	var Router = require("../../libs/router.js");
	var searchDbQuery = require("../../libs/search.js");
	var TaskPopoverCard = require("../../vue_components/task_popover_card.vue");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-conversations",
		data: () => {
			return {
				conversations: [
					{ id: 111, auth_address: "testAuth", title: "Toolbar and Navbar", body: "This task is almost finished. I'd like feedback on it.", type: "task_conversation", task: { task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "Toolbar and Navbar", body: `Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.`, done: true, menu: false, assigned: "krixano@kxoid.bit" } },
					{ id: 112, auth_address: "testAuth", title: "AES vs. ECIES", body: `Sed do eiusmod
						tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
						quis nostrud exercitation ullamco laboris.`, type: "task_assigned", task: { task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "AES Encryption", body: `We need to implement the option to use AES for projects so that projects can be private.`, done: false, menu: false },
						comments: [
							{ comment_id: 111, auth_address: "testAuth", cert_user_id: "krixano@kxoid.bit", body: "ECIES wouldn't work because we'd need to encrypt everything with multiple keys which would get slow if we want to allow adding multiple users per project." },
							{ comment_id: 111, auth_address: "testAuth2", cert_user_id: "kxobot@kxoid.bit", body: "AES is symmetric encryption, so with AES we'd only need one key." },
							{ comment_id: 112, auth_address: "testAuth", cert_user_id: "krixano@kxoid.bit", body: "Right! That's means we'd only need to encrypt everything once, with the shared key. I'll create and assign a task for this now." },
							{ comment_id: 113, auth_address: "testAuth", cert_user_id: "krixano@kxoid.bit", body: "This has been added. Now it needs testing in Prerelease #1." },
						], files: [
							{ title: "Prerelease #1", description: "This is a zip of all of the code of the very first prerelease.", id: 111, auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit" }
						] },
					{ id: 112, auth_address: "testAuth", title: "GitCenter Repository Interaction?", body: `Sed do eiusmod
						tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
						quis nostrud exercitation ullamco laboris.`, type: "no_task" }
				],
				activeConversation: "0",
				activeConversationIndex: null,
				filter: "all",
				task_popover_card: TaskPopoverCard
			};
		},
		beforeMount: function() {
			var self = this;

			this.$emit("setcallback", "update", function(userInfo) {
			});
		},
		computed: {
			isLoggedIn: function() {
				if (this.userInfo == null) return false;
				return this.userInfo.cert_user_id != null;
			}
		},
		methods: {
			getMarkdown: function(data) {
				return window.md.render(data);
			},
			goto: function(to) {
				Router.navigate(to);
			},
			login: function() {
				page.selectUser();
				return false;
			},
			gotoLink: function(to) {
				console.log(to);
				window.location = to;
			},
			showConversation: function(index) {
				const isActive = parseInt(this.activeConversation) != 0;
				if (isActive) {
					if (this.activeConversationIndex == index) {
						this.activeConversation = (0).toString();
						var self = this;
						setTimeout(function() {
							self.activeConversationIndex = null;
						}, 400);
					} else {
						this.activeConversation = (index + 1).toString();
						this.activeConversationIndex = index;
					}
				} else {
					this.activeConversationIndex = index;
					this.activeConversation = (index + 1).toString();
				}
			}
		}
	}
</script>