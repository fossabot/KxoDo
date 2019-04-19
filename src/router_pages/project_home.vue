<template><v-container style="margin: 0; padding: 0; height: 100%;" fluid>
	<v-container style="margin: 0; padding: 0;" fluid>
		<v-toolbar dark dense prominent extended>
			<v-layout style="padding-left: 50px; padding-right: 50px;">
			    <v-toolbar-title>KxoEncrypt</v-toolbar-title>
			    <v-spacer></v-spacer>
			</v-layout>
		    <!--<v-toolbar-items class="hidden-sm-and-down">
			    <v-btn flat>Link One</v-btn>
			    <v-btn flat>Link Two</v-btn>
			    <v-btn flat>Link Three</v-btn>
		    </v-toolbar-items>-->
		    <v-layout row slot="extension">
	            <v-tabs show-arrows dark align-with-title v-model="currentTab">
	                <v-tab ripple v-for="tab in tabs" :key="tab.key" :href="'#tab_' + tab.key">{{ tab.title }}</v-tab>
	            </v-tabs>
			</v-layout>
		</v-toolbar>
	</v-container>
	<v-container fluid style="height: 100%;">
		<v-tabs-items v-model="currentTab">
			<v-tab-item v-for="tab in tabs" :key="tab.key" :id="'tab_' + tab.key" lazy>
				<component ref="tab_view" :is="tab.component" :user-settings="userSettings" :user-info="userInfo" :lang-translation="langTranslation"></component>
			</v-tab-item>
		</v-tabs-items>
	</v-container>
</v-container></template>

<script>
	var Router = require("../libs/router.js");
	var searchDbQuery = require("../libs/search.js");

	var ProjectOverview = require("./project/overview.vue");
	var ProjectLists = require("./project/lists.vue");
	var ProjectConversations = require("./project/conversations.vue");
	var ProjectCalendar = require("./project/calendar.vue");
	var ProjectFiles = require("./project/files.vue");
	var ProjectQA = require("./project/qa.vue");
	var ProjectRepository = require("./project/repository.vue");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-home",
		data: () => {
			return {
				currentTab: null,
				tabs: [
					{ key: "overview", title: "Overview", component: ProjectOverview },
					{ key: "lists", title: "Lists", component: ProjectLists },
					{ key: "conversations", title: "Conversations", component: ProjectConversations },
					{ key: "calendar", title: "Calendar", component: ProjectCalendar },
					{ key: "files", title: "Files", component: ProjectFiles },
					{ key: "qa", title: "QA", component: ProjectQA },
					{ key: "repository", title: "Repository", component: ProjectRepository },
				]
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