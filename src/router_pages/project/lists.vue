<template>
	<v-container grid-list-xl>
		<v-layout row wrap fill-height>
			<v-flex xs12 sm8>
				<v-tabs-items v-model="activeList">
					<v-tab-item v-for="(list, list_index) in lists" :key="'list_' + list_index" lazy>
						<v-divider></v-divider>
						<v-subheader>{{ list.title }}</v-subheader>

						<v-list subheader v-if="list.taskGroups">
							<template v-for="taskGroup in list.taskGroups">
								<v-subheader :key="'group_subheader_' + taskGroup.task_group_auth_address + '_' + taskGroup.task_group_id">{{ taskGroup.title }}</v-subheader>
								<v-list-tile v-for="task in taskGroup.tasks" :key="'task_' + task.task_auth_address + '_' + task.task_id" avatar :class="{ 'done': task.done }" @click="showMenu($event, task)">
									<v-list-tile-action>
										<v-checkbox v-model="task.done" :value="task.done" @click.stop></v-checkbox>
									</v-list-tile-action>
									<v-list-tile-content>
										<v-list-tile-title v-html="(task.done ? '<s>' : '') + task.title + (task.done ? '</s>' : '')"></v-list-tile-title>
									</v-list-tile-content>
								</v-list-tile>
							</template>
						</v-list>

						<!-- Menu's -->
						<template v-for="taskGroup in list.taskGroups">
							<v-menu absolute :position-x="x" :position-y="y" offset-y :close-on-content-click="false" nudge-width="200" max-width="400" v-for="task in taskGroup.tasks" :key="" v-model="task.menu" lazy> <!-- Do I want this to be lazy? -->
								<component :is="task_popover_card" :user-settings="userSettings" :user-info="userInfo" :langTranslation="langTranslation" :task="task"></component>
							</v-menu>
						</template>
					</v-tab-item>
				</v-tabs-items>
			</v-flex>
			<v-flex xs12 sm4>
				<v-divider></v-divider>
				<v-subheader>Lists</v-subheader>

				<v-list dense subheader>
					<v-list-tile v-for="(list, index) in lists" :class="{ 'active': activeList == index }" @click="activateList(index)" :key="index">
						<v-list-tile-content>
							<v-list-tile-title>{{ list.title }}</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
				</v-list>

				<v-divider style="margin-top: 15px;"></v-divider>
				<v-subheader>Filter and Sort</v-subheader>
				<v-text-field prepend-icon="search" placeholder="Search" v-model="searchQuery"></v-text-field>
				<v-switch v-model="showingDone" :value="showingDone" label="Show Done"></v-switch>
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
</style>

<script>
	var Router = require("../../libs/router.js");
	var searchDbQuery = require("../../libs/search.js");
	var TaskPopoverCard = require("../../vue_components/task_popover_card.vue");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-lists",
		data: () => {
			return {
				searchQuery: "",
				lists: [
					{ title: "Basics", taskGroups: [
						{ task_group_id: 111, task_group_auth_address: "testAuth", cert_user_id: "krixano@kxoid.bit", title: "Basic Vuetify Layout", tasks: [
							{ task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "Toolbar and Navbar", body: `Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.`, done: true, menu: false, assigned: "krixano@kxoid.bit" },
							{ task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "Homepage", body: "", done: false, menu: false },
						] },
						{ task_group_id: 112, task_group_auth_address: "testAuth2", cert_user_id: "krixano@kxoid.bit", title: "Passwords", tasks: [
							{ task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "Add Password", body: "", done: false, menu: false,  },
							{ task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "Remove Password", body: "", done: false, menu: false },
							{ task_id: 111, task_auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit", title: "AES Encryption", body: `We need to implement the option to use AES for projects so that projects can be private.`, done: false, menu: false },
						] }
					 ] },
					{ title: "Files" },
					{ title: "Extra" },
				],
				activeList: "0",
				x: 0,
				y: 0,
				task_popover_card: TaskPopoverCard,
				showingDone: true
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
            showMenu: function(e, task) {
				e.preventDefault()
				console.log("Task: ", task);
				task.menu = false;
		        this.x = e.clientX
				this.y = e.clientY
				var self = this;
		        this.$nextTick(() => {
				  task.menu = true;
		        })
			},
			/*showMenu2: function(e) {
            	e.preventDefault()
		        this.testMenu2 = false
		        this.x = e.clientX
		        this.y = e.clientY
		        this.$nextTick(() => {
		          this.testMenu2 = true
		        })
			},*/
			activateList: function(index) {
				this.activeList = index.toString();
			}
		}
	}
</script>