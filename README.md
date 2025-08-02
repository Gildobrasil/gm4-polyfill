// ==UserScript==
// @name         VFS Global Auto Login e Auto-Fill com Menu Moderno e Impactante
// @namespace    https://visa.vfsglobal.com
// @version      1.9
// @description  Auto login e preenchimento de formulário para VFS Global com menu moderno e impactante
// @author       eugenio
// @match        https://visa.vfsglobal.com/*
// @grant        none
// ==/UserScript==

(function () {
    'use strict';

    // Perfis de login
    const profilesLogin = [

        {
            name: "geniusedits99",
            email: "gildoeubrasil@gmail.com",
            password: "Gi922844$"
        },
   {
            name: "Kiala",
            email: "gildoeubrasil@gmail.com",
            password: "Gi922844$"
        },
    ];

    // Perfis de formulário
    const profilesForm = [
        // Perfis Nacionais

        {
            type: "nacional",
            firstName: "KIALA",
            lastName: "MAYISANDA",
            dob: "10/03/1973",
            gender: "Male",
            passportNumber: "N2665526",
            passportExpiry: "28/01/2031",
            nationality: "ANGOLA"
        },
          // Perfis Schengen
        {
            type: "schengen",
            firstName: "victoria",
            lastName: "Lukubu",
            dob: "30/08/2005",
            gender: "Female",
            passportNumber: "N3499149",
            passportExpiry: "13/11/2034",
            nationality: "ANGOLA"
        },
        {
            type: "schengen",
            firstName: "LUISA",
            lastName: "MARTINS",
            dob: "01/03/2018",
            gender: "Female",
            passportNumber: "N3453873",
            passportExpiry: "07/02/2034",
            nationality: "ANGOLA"
        },

    ];

    const defaultContactNumber = "935439638";
    //const defaultEmail = "eugeniodeamaral6@gmail.com";

    // Função para preencher automaticamente o formulário
    function preencherCampoPorPlaceholder(placeholder, valor) {
        const input = Array.from(document.querySelectorAll('input')).find(el => el.placeholder.includes(placeholder));
        if (input) {
            input.value = valor;
            input.dispatchEvent(new Event('input'));
            input.dispatchEvent(new Event('blur'));
        }
    }

    function selecionarOpcaoPorTexto(menuPlaceholder, opcaoTexto) {
        const menu = Array.from(document.querySelectorAll('div')).find(el => el.textContent.includes(menuPlaceholder));
        if (menu) {
            menu.click();
            setTimeout(() => {
                const opcao = Array.from(document.querySelectorAll('mat-option')).find(el => el.textContent.trim() === opcaoTexto);
                if (opcao) opcao.click();
            }, 500);
        }
    }

    function selecionarGenero(genero) {
        const menu = document.querySelector('[id^="mat-select-value"]');
        if (menu) {
            menu.click();
            setTimeout(() => {
                const opcao = Array.from(document.querySelectorAll('mat-option')).find(el => el.textContent.trim() === genero);
                if (opcao) opcao.click();
            }, 500);
        }
    }

    function selecionarNacionalidade(nacionalidade) {
        const menus = document.querySelectorAll('[id^="mat-select-value"]');
        if (menus.length > 1) {
            menus[1].click(); // Assume o segundo menu é para nacionalidade
            setTimeout(() => {
                const opcao = Array.from(document.querySelectorAll('mat-option')).find(el => el.textContent.trim() === nacionalidade);
                if (opcao) opcao.click();
            }, 500);
        }
    }

    function preencherData(id, data) {
        const input = document.querySelector(`#${id}`);
        if (input) {
            input.value = data;
            input.dispatchEvent(new Event('input'));
            input.dispatchEvent(new Event('blur'));
        }
    }

    // Preenche o formulário com as informações do perfil
    function fillForm(profile, email) {
        try {
            preencherCampoPorPlaceholder('Enter your first name', profile.firstName);
            preencherCampoPorPlaceholder('Please enter last name.', profile.lastName);
            selecionarGenero(profile.gender);
            selecionarNacionalidade(profile.nationality);
            preencherCampoPorPlaceholder('Enter passport number', profile.passportNumber);
            preencherCampoPorPlaceholder('44', '244');
            preencherCampoPorPlaceholder('012345648382', defaultContactNumber);
            preencherCampoPorPlaceholder('Enter Email Address', email); // Use the login email here
            preencherData('dateOfBirth', profile.dob);
            preencherData('passportExpirtyDate', profile.passportExpiry);
        } catch (err) {
            console.error("Erro ao preencher o formulário:", err);
        }
    }

    // Função para preencher e submeter o login
    function fillAndSubmitLogin(email, password) {
        // Função para selecionar campos dinamicamente por atributos comuns
        function getInputByPlaceholder(placeholder) {
            return Array.from(document.querySelectorAll('input')).find(input => input.placeholder && input.placeholder.includes(placeholder));
        }

        // Preenche o campo de email
        const emailInput = document.querySelector('input[type="email"]') || getInputByPlaceholder('email');
        if (emailInput) {
            emailInput.value = email;
            emailInput.dispatchEvent(new Event('input', { bubbles: true }));
        } else {
            console.error('Campo de email não encontrado');
        }

        // Preenche o campo de senha
        const passwordInput = document.querySelector('input[type="password"]');
        if (passwordInput) {
            passwordInput.value = password;
            passwordInput.dispatchEvent(new Event('input', { bubbles: true }));
        } else {
            console.error('Campo de senha não encontrado');
        }

        // Simula o clique no botão de login baseado no span com a classe mat-mdc-button-touch-target
        const loginButton = document.querySelector('span.mat-mdc-button-touch-target');
        if (loginButton) {
            loginButton.closest('button').click(); // Clica no botão pai que contém o span
        } else {
            console.error('Botão de login não encontrado');
        }

        // Alteração: Adiciona um sinal de bom ao email selecionado de forma mais confiável
        document.querySelectorAll('.login-email-selected').forEach(el => el.classList.remove('login-email-selected'));
        const loginButtons = document.querySelectorAll('#side-menu button');
        let selectedButton = null;
        const emailIdentifier = email.split('@')[0].toLowerCase();
        loginButtons.forEach(btn => {
            if (
                btn.textContent.toLowerCase().includes('login como') &&
                btn.textContent.toLowerCase().includes(emailIdentifier)
            ) {
                selectedButton = btn;
            }
        });
        if (selectedButton) {
            selectedButton.classList.add('login-email-selected');
        } else {
            console.warn('Nenhum botão de login encontrado para o email: ' + email);
        }
    }

    // Criar botão do menu lateral
    const menuButton = document.createElement('button');
    menuButton.id = "login-menu-button";
    menuButton.textContent = "Abrir Menu";
    menuButton.style.position = "fixed";
    menuButton.style.top = "20px"; // Ajustado para a parte superior
    menuButton.style.right = "20px"; // Ajustado para a parte direita
    menuButton.style.padding = "12px 24px";
    menuButton.style.backgroundColor = "#FF7043"; // Cor laranja para destaque
    menuButton.style.color = "#FFFFFF";
    menuButton.style.border = "none";
    menuButton.style.borderRadius = "10px"; // Borda arredondada para dar um toque moderno
    menuButton.style.cursor = "pointer";
    menuButton.style.fontSize = "16px";
    menuButton.style.zIndex = "10000"; // Alto z-index para sobrepor
    menuButton.style.transition = "background-color 0.3s, transform 0.3s";

    menuButton.addEventListener('mouseover', () => {
        menuButton.style.backgroundColor = "#FF5722"; // Tom de laranja mais forte no hover
        menuButton.style.transform = "scale(1.05)";
    });

    menuButton.addEventListener('mouseout', () => {
        menuButton.style.backgroundColor = "#FF7043";
        menuButton.style.transform = "scale(1)";
    });

    // Criar o menu deslizante
    const menu = document.createElement('div');
    menu.id = "side-menu";
    menu.style.position = "fixed";
    menu.style.top = "80px"; // Ajustado para estar acima do meio
    menu.style.right = "20px"; // Ajustado para a parte direita
    menu.style.backgroundColor = "#FFFFFF";
    menu.style.border = "1px solid #DDD";
    menu.style.borderRadius = "10px"; // Bordas arredondadas para o menu
    menu.style.boxShadow = "0 6px 12px rgba(0, 0, 0, 0.1)";
    menu.style.zIndex = "10001"; // Mais alto para sobrepor o conteúdo
    menu.style.padding = "20px";
    menu.style.display = "none";
    menu.style.width = "350px"; // Largura do menu aumentada para ficar mais espaçoso
    menu.style.height = "auto";
    menu.style.maxHeight = "70vh";
    menu.style.overflowY = "auto";
    menu.style.transition = "transform 0.3s ease-in-out";
    menu.style.transform = "translateX(100%)"; // Inicialmente fora da tela

    // Estilo dos botões do menu
    function styleProfileButton(button, backgroundColor, hoverColor) {
        button.style.display = "block";
        button.style.marginBottom = "15px";
        button.style.width = "100%";
        button.style.padding = "15px";
        button.style.backgroundColor = backgroundColor;
        button.style.border = "none";
        button.style.borderRadius = "8px";
        button.style.color = "#FFF";
        button.style.fontSize = "18px";
        button.style.fontWeight = "bold";
        button.style.cursor = "pointer";
        button.style.transition = "background-color 0.3s, transform 0.3s";
        button.style.boxShadow = "0 4px 6px rgba(0, 0, 0, 0.1)";

        button.addEventListener('mouseover', () => {
            button.style.backgroundColor = hoverColor;
            button.style.transform = "scale(1.05)";
        });

        button.addEventListener('mouseout', () => {
            button.style.backgroundColor = backgroundColor;
            button.style.transform = "scale(1)";
        });
    }

    // Adiciona estilo para o sinal de bom
    const style = document.createElement('style');
    style.textContent = `
        .login-email-selected::after {
            content: '✔';
            color: green;
            margin-left: 10px;
            font-size: 18px;
        }
    `;
    document.head.appendChild(style);

    // Adicionando os perfis de login ao menu
    profilesLogin.forEach((profile) => {
        const profileButton = document.createElement('button');
        profileButton.textContent = `Login como ${profile.name}`;
        styleProfileButton(profileButton, "#FF7043", "#FF5722"); // Cor laranja para login

        profileButton.addEventListener('click', () => {
            menu.style.display = "none";
            menu.style.transform = "translateX(100%)"; // Fecha o menu
            fillAndSubmitLogin(profile.email, profile.password);
            // Store the login email to use it later in form filling
            window.currentLoginEmail = profile.email;
        });

        menu.appendChild(profileButton);
    });

    // Adicionando os perfis de formulário ao menu
    profilesForm.forEach((profile) => {
        const profileButton = document.createElement('button');
        profileButton.textContent = `Preencher Formulário (${profile.type}) para ${profile.firstName} ${profile.lastName}`;
        styleProfileButton(profileButton, profile.type === "nacional" ? "#4CAF50" : "#2196F3", profile.type === "nacional" ? "#388E3C" : "#1976D2"); // Cor verde para nacionais e azul para schengen

        profileButton.addEventListener('click', () => {
            menu.style.display = "none";
            menu.style.transform = "translateX(100%)"; // Fecha o menu
            fillForm(profile, window.currentLoginEmail); // Pass the current login email
        });

        menu.appendChild(profileButton);
    });

    // Adicionar os botões ao corpo da página
    document.body.appendChild(menuButton);
    document.body.appendChild(menu);

    // Função para abrir o menu
    menuButton.addEventListener('click', () => {
        if (menu.style.display === "none") {
            menu.style.display = "block";
            menu.style.transform = "translateX(0)";
        } else {
            menu.style.transform = "translateX(100%)";
            setTimeout(() => {
                menu.style.display = "none";
            }, 300);
        }
    });

})();
