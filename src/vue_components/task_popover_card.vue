<template>
	<v-card>
		<v-card-title primary-title v-if="!conversationType">{{ task.title }}</v-card-title>
		<v-card-text>
			<span v-if="conversationType" v-html="conversationType == 'task_assigned' ? '<strong>Assigned Task: ' + task.title + '</strong><br>' : ''"></span>
			<span class="markdown" v-html="getMarkdown(task.body || '')"></span>
			<small>
				<span v-if="task.assigned">Assigned to <span>{{ task.assigned }}</span><br></span>
				Due in 3 days.
			</small>
		</v-card-text>
		<v-card-actions>
			<v-btn flat color="yellow" v-if="!conversationType">Goto Conversation</v-btn>
			<v-btn flat color="yellow" v-else>{{ task.done ? 'Unmark' : 'Mark Done' }}</v-btn>
			<v-btn flat color="yellow">Assigned Conversations</v-btn>
			<!--<v-btn flat color="yellow">Start Conversation</v-btn>-->
		</v-card-actions>
	</v-card>
</template>

<script>
	var Router = require("../libs/router.js");
	var searchDbQuery = require("../libs/search.js");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation", "task", "conversationType"],
		name: "task-popover-card",
		data: () => {
			return {
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
            }
		}
	}
</script>