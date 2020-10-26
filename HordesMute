// ==UserScript==
// @name         Hordes Mute
// @namespace    http://tampermonkey.net/
// @version      0.0.4
// @description  Because why not
// @author       Eliam (©Killboy)
// @match        http://www.hordes.fr/*
// @icon         http://www.hordes.fr/gfx/icons/item_bullets.gif
// @grant        none
// @require      https://tmp-staticserver.herokuapp.com/lib/KHLib-0.4.2.js
// ==/UserScript==

// A MODIFIER.
var mute = ["Ross","pheb"]; // Exemple var mute = ["Pseudo1", "Pseudo2", "Pseudo3"];

(function() {
    "use strict";

    const KhLib = window.KhLib.core.copy();
    KhLib.checkDependencies(KhLib, ["onGameUpdate", "dom"], "KhLib");
    const dom = KhLib.dom;

    const state = {
        initialized: false,
        forumInit: false,
    };

    const store = KhLib.createStore("huu", {
        uninstalled: []
    });

    const displayNotification = (text, action) => {
        dom.query("#notificationText").text(text);
        if (action) {
            dom.query("#notification .button").on("click", action)
        }
        dom.query("#notification").addClass("showNotif");
    };

    function getButton1() {

        function uninstallUser() {
            for(let i = 0; i < mute.length; i++) {
                const name=mute[i]
                store.set({
                    uninstalled: [...store.get().uninstalled, {id: mute[i], name}]
                })
            };
            displayNotification(`Désinstallation des emmerdeurs terminé.\nProfitez de votre Hordes sans virus.`, () => location.reload())
        }
        const button = dom.query(`<a class="button" >Désinstallation des <strong>emmerdeurs</strong></a>`);
        button.on("click", uninstallUser);
        return button;
    }

    function getButton2() {

        function installUser() {
            store.set({
                uninstalled: []
            })
            displayNotification(`Installation des emmerdeurs terminée.\nProfitez de votre Hordes avec virus.`, () => location.reload())
        }
        const button = dom.query(`<a class="button" >Installation des <strong>emmerdeurs</strong></a>`);
        button.on("click", installUser);
        return button;
    }

    function uninstallAll() {
        store.get().uninstalled.forEach(user => {
            console.log("Uninstall", user.name);
            const re = new RegExp(user.name, "g");

            dom.query(`.tid_user:contains('${user.name}')`).each(function(){
                const it = dom.query(this);

                if (window.location.hash.startsWith("#!view/")) {
                    it.parent().parent().parent().css("opacity", "1%");
                    it.parent().parent().css("display", "none");
                    it.parent().parent().parent().css("max-height", "30px");
                    it.parent().parent().parent().css("overflow", "hidden");
                } else {
                    it.html(it.html().replace(re, "Désinstallé"))
                }
            });

            if (!window.location.hash.startsWith("#!view/")) {
                dom.query(`.entry strong:contains('${user.name}')`).html("Désintallé")
                dom.query(`.tid_avatarImg[alt='${user.name}']`).attr("src", "")

                dom.query(`#gamebody:contains('${user.name}')`).each(function() {
                    const it = dom.query(this);
                    it.html(it.html().replace(re, "Désinstallé"));
                });
            }
        });
    }

    const refresh = () => {
        uninstallAll();
        if (dom.query(".misc .button").length) {
            dom.query(".misc .button").after(getButton1).after(getButton2);
        }
    };

    const tryBindToForum = () => {
        if (!state.forumInit && !state.forumIsLoading) {

            state.forumIsLoading = true;
            KhLib.onForumUpdate(uninstallAll).then(() => {
                state.forumInit = true;
            }).catch(() => {
                state.forumIsLoading = false;
            });
        }
    }

    const load = () => {
        if (!state.initialized) {
            KhLib.onHashChange(tryBindToForum);
            KhLib.onGameUpdate(refresh);

            state.initialized = true;
            refresh();
        }
    }
    KhLib.ready(() => {
        load();
    });
})();
