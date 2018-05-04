<template>
	<q-layout view="hHr LpR lFf">
		<q-layout-header>
			<q-toolbar>
				<q-toolbar-title>
					Bill Tracker
					<!-- <button @click="enableNotifications">Notifications</button> -->
				</q-toolbar-title>
			</q-toolbar>
		</q-layout-header>

		<q-page-container class='scroll-content'>
			<q-page>

				<div v-if='billers.length > 0'>

					<!-- unpaid bills -->
					<div v-for="biller in billers" :key="biller.id">
						<div v-if='!biller.isPaid'>
							<card-comp :propbiller="biller"></card-comp>
						</div>
					</div>

					<hr>

					<!-- paid bills -->
					<div v-for="biller in billers" :key="biller.title">
						<div v-if='biller.isPaid'>
							<card-comp :propbiller="biller"></card-comp>
						</div>
					</div>

				</div>
				<div v-else>
					No bills to show right now.
				</div>
			</q-page>
		</q-page-container>
	</q-layout>
</template>

<script>
var config = {
	apiKey: "AIzaSyCJXGDc2kvyJSOSzu_hvhS64sHDnyKn_qE",
	authDomain: "billersapp.firebaseapp.com",
	databaseURL: "https://billersapp.firebaseio.com",
	projectId: "billersapp",
	storageBucket: "billersapp.appspot.com",
	messagingSenderId: "5384173140"
};

firebase.initializeApp(config);

module.exports = {
	components: {
		"card-comp": httpVueLoader("components/card-component.vue")
	},
	firestore() {
		return {
			billers: firebase
				.firestore()
				.collection("billers")
				.orderBy("dayOfMonth", "asc")
		};
	},
	data() {
		return {
			message: "Vuefire & Firestore",
			notificationSupported: false
		};
	},
	created() {
		this.enableNotifications()
	},
	methods: {

		enableNotifications() {
			if ("Notification" in window) {

				if (Notification.permission === 'granted') {
					return
				}

				Notification.requestPermission()
					.then(res => {
						console.log(res)
						if (res !== 'granted') {
							console.log('permission was denied for notifications')
							//alert('denied...')
						} else {

							navigator.serviceWorker.ready.then(function (registration) {
								registration.showNotification('Notification test was successful', {
									body: '$1',
									icon: 'http://icons.iconarchive.com/icons/dapino/money/256/Purse-icon.png',
									vibrate: [200, 100, 200, 100, 200, 100, 200],
									badge: 'http://icons.iconarchive.com/icons/dapino/money/256/Purse-icon.png'
									// image: 'http://www.leveragedloan.com/wp-content/uploads/2018/01/netflix-logo.png'
								});
							});

							// alert('notificiation permission granted')
							// navigator.serviceWorker.ready.then(function (registration) {
							// 	registration.showNotification('Netflix is coming up', {
							// 		body: '$1475',
							// 		icon: 'http://icons.iconarchive.com/icons/dapino/money/256/Purse-icon.png',
							// 		vibrate: [200, 100, 200, 100, 200, 100, 200],
							// 		badge: 'http://icons.iconarchive.com/icons/dapino/money/256/Purse-icon.png'
							// 		// image: 'http://www.leveragedloan.com/wp-content/uploads/2018/01/netflix-logo.png'
							// 	});
							// });


						}
					})

			} else {
				//alert("This browser does not support notifications, I am sorry :/")
				console.log('This browser does not support notifications :/')
			}
		},
		addBiller() {

			console.log("adding test biller");
			this.$firestore.billers.add({
				title: "Test new",
				amount: 0,
				isPaid: false,
				dayOfMonth: 2,
				imageUrl: ""
			});
		},
		deleteBiller(biller) {
			this.$firestore.billers.doc(biller[".key"]).delete();
		},
		presentAddModal() { },
		presentMoneyOverviewModal() { }
	}
};
</script>

<style lang='css'>
.scroll-content {
	left: 0;
	right: 0;
	top: 0;
	bottom: 0;
	position: absolute;
	z-index: 1;
	display: block;
	overflow-x: hidden;
	overflow-y: scroll;
	-webkit-overflow-scrolling: touch;
	will-change: scroll-position;
	contain: size style layout;
	background: linear-gradient(45deg, #6cfd9f, #6887ff);
}
</style>