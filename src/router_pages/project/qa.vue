<template>
	<v-container grid-list-xl>
		<v-layout row wrap fill-height>
			<v-flex xs12 sm7>
				<v-divider></v-divider>
				<v-subheader>Questions</v-subheader>

				<v-list>
					<template v-for="(question, index) in questions">
						<v-list-tile :key="question.auth_address + '_' + question.id" avatar :class="{ 'active': activeQuestionIndex == index }" @click="showQuestion(index)">
							<v-list-tile-content>
								<v-list-tile-title>{{ question.title }}</v-list-tile-title>
							</v-list-tile-content>
						</v-list-tile>
					</template>
				</v-list>
			</v-flex>
			<v-flex xs12 sm5>
				<v-tabs-items v-model="activeQuestion">
					<v-tab-item key="0">
						<v-divider></v-divider>
						<v-subheader>Filter and Sort</v-subheader>
						<v-text-field prepend-icon="search" placeholder="Search" v-model="searchQuery"></v-text-field>
						<v-switch v-model="showingSolved" :value="showingSolved" label="Show Solved"></v-switch>
					</v-tab-item>
					<v-tab-item v-for="(question, index) in questions" :key="index" lazy>
						<div v-if="activeQuestionIndex != null">
							<v-divider></v-divider>
							<v-subheader>{{ question.title }}</v-subheader>
							<div style="padding-left: 16px; padding-right: 16px;">
								<span v-html="getMarkdown(question.body)"></span>
								<v-btn flat color="blue">Answer</v-btn>
							</div>

							<v-divider style="margin-top: 15px;"></v-divider>
							<v-subheader>Answers</v-subheader>
							<div style="padding-left: 16px; padding-right: 16px;"></div>
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

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-qa",
		data: () => {
			return {
				questions: [
					{ id: 111, auth_address: "testAuth", title: "What is KxoEncrypt?", body: `Sed do eiusmod
						tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
						quis nostrud exercitation ullamco laboris.` },
					{ id: 222, auth_address: "testAuth", title: "How secure is KxoEncrypt?", body: `Ut enim ad minim veniam,
						quis nostrud exercitation ullamco laboris. Sed do eiusmod
						tempor incididunt ut labore et dolore magna aliqua.` },
				],
				activeQuestion: "0",
				activeQuestionIndex: null,
				showingSolved: true
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
			showQuestion: function(index) {
				const isActive = parseInt(this.activeQuestion) != 0;
				if (isActive) {
					if (this.activeQuestionIndex == index) {
						this.activeQuestion = (0).toString();
						var self = this;
						setTimeout(function() {
							self.activeQuestionIndex = null;
						}, 400);
					} else {
						this.activeQuestionIndex = index;
						this.activeQuestion = (index + 1).toString();
					}
				} else {
					this.activeQuestionIndex = index;
					this.activeQuestion = (index + 1).toString();
				}
			}
		}
	}
</script>