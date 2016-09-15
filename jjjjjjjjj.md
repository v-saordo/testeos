#### <a name="categories"></a> Catégories

Quand vous modifiez les dispositions fournies, vous modifiez l'apparence de toutes vos notifications. Les catégories permettent de définir diverses recherches ciblées (et éventuellement des comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne Reach. N'oubliez pas que les catégories vous permettent également de personnaliser les annonces et les sondages, décrits plus avant dans ce document.

Pour inscrire un gestionnaire de catégories pour vos notifications, vous devez ajouter un appel quand l'application est initialisée.

> [AZURE.IMPORTANT] Lisez l’avertissement concernant l’attribut android:process \<android-sdk-engagement-process\> dans la rubrique « Comment intégrer Engagement sur Android » avant de continuer.

L'exemple suivant suppose que vous avez pris en compte l'avertissement précédent et que vous utilisez une sous-classe de `EngagementApplication` :

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
			  }
			}

L'objet `MyNotifier` est l'implémentation du gestionnaire de catégories des notifications. Il s'agit soit d'une implémentation de l'interface `EngagementNotifier`, soit d’une sous-classe de l'implémentation par défaut : `EngagementDefaultNotifier`.

Notez que le même notificateur peut gérer plusieurs catégories. Vous pouvez les inscrire de la façon suivante :

			reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Pour remplacer l'implémentation de catégorie par défaut, vous pouvez inscrire votre implémentation comme dans l'exemple suivant :

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
			  }
			}

La catégorie utilisée actuellement dans le gestionnaire est passée en tant que paramètre dans la plupart des méthodes que vous pouvez remplacer dans `EngagementDefaultNotifier`.
