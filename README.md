# Chatiava
My cutie app
const ComponentFunction = function() {
  // @section:imports @depends:[]
  const React = require('react');
  const { useState, useEffect, useContext, useMemo, useCallback } = React;
  const { View, Text, StyleSheet, ScrollView, TouchableOpacity, TextInput, Modal, Alert, Platform, StatusBar, ActivityIndicator, KeyboardAvoidingView, FlatList } = require('react-native');
  const { MaterialIcons } = require('@expo/vector-icons');
  const { createBottomTabNavigator } = require('@react-navigation/bottom-tabs');
  // @end:imports

  // @section:theme @depends:[]
  const storageStrategy = 'local';
  const primaryColor = '#FF6B9D';
  const accentColor = '#FF8A65';
  const backgroundColor = '#FFF8F8';
  const cardColor = '#FFFFFF';
  const textPrimary = '#2D1B2E';
  const textSecondary = '#8E7A8F';
  const designStyle = 'playful';
  // @end:theme

  // @section:navigation-setup @depends:[]
  const Tab = createBottomTabNavigator();
  // @end:navigation-setup

  // @section:ThemeContext @depends:[theme]
  const ThemeContext = React.createContext();
  const ThemeProvider = function(props) {
    const darkModeState = useState(false);
    const darkMode = darkModeState[0];
    const setDarkMode = darkModeState[1];
    
    const lightTheme = useMemo(function() {
      return {
        colors: {
          primary: primaryColor,
          accent: accentColor,
          background: backgroundColor,
          card: cardColor,
          textPrimary: textPrimary,
          textSecondary: textSecondary,
          border: '#F0E6F3',
          success: '#4CAF50',
          error: '#F44336',
          warning: '#FF9800'
        }
      };
    }, []);
    
    const darkTheme = useMemo(function() {
      return {
        colors: {
          primary: primaryColor,
          accent: accentColor,
          background: '#2D1B2E',
          card: '#4A3A4F',
          textPrimary: '#F9FAFB',
          textSecondary: '#D1D5DB',
          border: '#6B5B70',
          success: '#4CAF50',
          error: '#F44336',
          warning: '#FF9800'
        }
      };
    }, []);
    
    const theme = darkMode ? darkTheme : lightTheme;
    
    const toggleDarkMode = useCallback(function() {
      setDarkMode(function(prev) { return !prev; });
    }, []);
    
    const value = useMemo(function() {
      return { 
        theme: theme, 
        darkMode: darkMode, 
        toggleDarkMode: toggleDarkMode, 
        designStyle: designStyle 
      };
    }, [theme, darkMode, toggleDarkMode]);
    
    return React.createElement(ThemeContext.Provider, { value: value }, props.children);
  };
  
  const useTheme = function() { 
    return useContext(ThemeContext); 
  };
  // @end:ThemeContext

  // @section:AvatarScreen-state @depends:[ThemeContext]
  const useAvatarScreenState = function() {
    const themeContext = useTheme();
    const theme = themeContext.theme;
    
    const avatarState = useState({
      style: 'animal',
      color: '#FF6B9D',
      accessory: 'none',
      nickname: ''
    });
    const avatar = avatarState[0];
    const setAvatar = avatarState[1];
    
    const showModalState = useState(false);
    const showModal = showModalState[0];
    const setShowModal = showModalState[1];
    
    const ageConfirmedState = useState(false);
    const ageConfirmed = ageConfirmedState[0];
    const setAgeConfirmed = ageConfirmedState[1];
    
    return {
      theme: theme,
      avatar: avatar,
      setAvatar: setAvatar,
      showModal: showModal,
      setShowModal: setShowModal,
      ageConfirmed: ageConfirmed,
      setAgeConfirmed: setAgeConfirmed
    };
  };
  // @end:AvatarScreen-state

  // @section:AvatarScreen-handlers @depends:[AvatarScreen-state]
  const avatarScreenHandlers = {
    updateAvatar: function(state, field, value) {
      state.setAvatar(function(prev) {
        const newAvatar = {};
        Object.keys(prev).forEach(function(key) {
          newAvatar[key] = prev[key];
        });
        newAvatar[field] = value;
        return newAvatar;
      });
    },
    
    saveProfile: function(state) {
      if (!state.avatar.nickname.trim()) {
        Platform.OS === 'web' ? window.alert('Please enter a nickname') : Alert.alert('Error', 'Please enter a nickname');
        return;
      }
      if (!state.ageConfirmed) {
        state.setShowModal(true);
        return;
      }
      Platform.OS === 'web' ? window.alert('Avatar saved successfully!') : Alert.alert('Success', 'Avatar saved successfully!');
    },
    
    confirmAge: function(state) {
      state.setAgeConfirmed(true);
      state.setShowModal(false);
      Platform.OS === 'web' ? window.alert('Avatar saved successfully!') : Alert.alert('Success', 'Avatar saved successfully!');
    }
  };
  // @end:AvatarScreen-handlers

  // @section:AvatarScreen-AgeModal @depends:[styles]
  const renderAgeConfirmationModal = function(visible, onConfirm, onCancel, theme) {
    return React.createElement(Modal, {
      visible: visible,
      animationType: 'slide',
      transparent: true,
      onRequestClose: onCancel
    },
      React.createElement(View, { 
        style: styles.modalOverlay,
        componentId: 'avatar-age-modal-overlay'
      },
        React.createElement(View, { 
          style: [styles.modalContent, { backgroundColor: theme.colors.card }],
          componentId: 'avatar-age-modal-content'
        },
          React.createElement(Text, { 
            style: [styles.modalTitle, { color: theme.colors.textPrimary }],
            componentId: 'avatar-age-modal-title'
          }, '🔞 Age Verification'),
          React.createElement(Text, { 
            style: [styles.modalText, { color: theme.colors.textSecondary }],
            componentId: 'avatar-age-modal-text'
          }, 'You must be 18 or older to use ChatiAva. This app is designed for adult conversations and interactions.'),
          React.createElement(View, { 
            style: styles.modalButtons,
            componentId: 'avatar-age-modal-buttons'
          },
            React.createElement(TouchableOpacity, {
              style: [styles.modalButton, { backgroundColor: theme.colors.border }],
              onPress: onCancel,
              componentId: 'avatar-age-modal-cancel'
            },
              React.createElement(Text, { 
                style: { color: theme.colors.textSecondary },
                componentId: 'avatar-age-modal-cancel-text'
              }, 'Under 18')
            ),
            React.createElement(TouchableOpacity, {
              style: [styles.modalButton, { backgroundColor: theme.colors.primary }],
              onPress: onConfirm,
              componentId: 'avatar-age-modal-confirm'
            },
              React.createElement(Text, { 
                style: { color: '#FFFFFF', fontWeight: 'bold' },
                componentId: 'avatar-age-modal-confirm-text'
              }, '18+ Confirmed')
            )
          )
        )
      )
    );
  };
  // @end:AvatarScreen-AgeModal

  // @section:AvatarScreen @depends:[AvatarScreen-state,AvatarScreen-handlers,AvatarScreen-AgeModal,styles]
  const AvatarScreen = function() {
    const state = useAvatarScreenState();
    const handlers = avatarScreenHandlers;
    
    const avatarStyles = ['animal', 'fantasy', 'robot', 'human'];
    const avatarColors = ['#FF6B9D', '#9C27B0', '#2196F3', '#4CAF50', '#FF9800'];
    const accessories = ['none', 'glasses', 'hat', 'bow', 'flower'];
    
    return React.createElement(KeyboardAvoidingView, {
      style: { flex: 1 },
      behavior: Platform.OS === 'ios' ? 'padding' : (Platform.OS === 'web' ? undefined : 'height')
    },
      React.createElement(ScrollView, {
        style: [styles.container, { backgroundColor: state.theme.colors.background }],
        contentContainerStyle: { paddingBottom: Platform.OS === 'web' ? 90 : 100 },
        componentId: 'avatar-screen-scroll'
      },
        React.createElement(View, { 
          style: styles.header,
          componentId: 'avatar-screen-header'
        },
          React.createElement(Text, { 
            style: [styles.headerTitle, { color: state.theme.colors.textPrimary }],
            componentId: 'avatar-screen-title'
          }, '✨ Create Your Avatar'),
          React.createElement(Text, { 
            style: [styles.headerSubtitle, { color: state.theme.colors.textSecondary }],
            componentId: 'avatar-screen-subtitle'
          }, 'Design your cute avatar to start chatting!')
        ),
        
        React.createElement(View, { 
          style: [styles.avatarPreview, { backgroundColor: state.theme.colors.card }],
          componentId: 'avatar-preview-card'
        },
          React.createElement(View, { 
            style: [styles.avatarDisplay, { backgroundColor: state.avatar.color }],
            componentId: 'avatar-display'
          },
            React.createElement(Text, { 
              style: styles.avatarEmoji,
              componentId: 'avatar-emoji'
            }, state.avatar.style === 'animal' ? '🐾' : state.avatar.style === 'fantasy' ? '🦄' : state.avatar.style === 'robot' ? '🤖' : '👤')
          ),
          React.createElement(Text, { 
            style: [styles.avatarName, { color: state.theme.colors.textPrimary }],
            componentId: 'avatar-name-display'
          }, state.avatar.nickname || 'Your Avatar')
        ),
        
        React.createElement(View, { 
          style: [styles.customizationCard, { backgroundColor: state.theme.colors.card }],
          componentId: 'avatar-customization-card'
        },
          React.createElement(Text, { 
            style: [styles.sectionTitle, { color: state.theme.colors.textPrimary }],
            componentId: 'avatar-style-title'
          }, 'Avatar Style'),
          React.createElement(View, { 
            style: styles.optionsRow,
            componentId: 'avatar-style-options'
          },
            avatarStyles.map(function(style, index) {
              const isSelected = state.avatar.style === style;
              return React.createElement(TouchableOpacity, {
                key: style,
                style: [
                  styles.optionButton,
                  { backgroundColor: isSelected ? state.theme.colors.primary : state.theme.colors.border }
                ],
                onPress: function() {
                  handlers.updateAvatar(state, 'style', style);
                },
                componentId: 'avatar-style-option-' + index
              },
                React.createElement(Text, { 
                  style: { 
                    color: isSelected ? '#FFFFFF' : state.theme.colors.textSecondary,
                    fontWeight: isSelected ? 'bold' : 'normal'
                  },
                  componentId: 'avatar-style-text-' + index
                }, style.charAt(0).toUpperCase() + style.slice(1))
              );
            })
          ),
          
          React.createElement(Text, { 
            style: [styles.sectionTitle, { color: state.theme.colors.textPrimary, marginTop: 20 }],
            componentId: 'avatar-color-title'
          }, 'Color'),
          React.createElement(View, { 
            style: styles.optionsRow,
            componentId: 'avatar-color-options'
          },
            avatarColors.map(function(color, index) {
              const isSelected = state.avatar.color === color;
              return React.createElement(TouchableOpacity, {
                key: color,
                style: [
                  styles.colorOption,
                  { backgroundColor: color, borderWidth: isSelected ? 3 : 1, borderColor: isSelected ? state.theme.colors.textPrimary : state.theme.colors.border }
                ],
                onPress: function() {
                  handlers.updateAvatar(state, 'color', color);
                },
                componentId: 'avatar-color-option-' + index
              });
            })
          ),
          
          React.createElement(Text, { 
            style: [styles.sectionTitle, { color: state.theme.colors.textPrimary, marginTop: 20 }],
            componentId: 'avatar-accessory-title'
          }, 'Accessory'),
          React.createElement(View, { 
            style: styles.optionsRow,
            componentId: 'avatar-accessory-options'
          },
            accessories.map(function(accessory, index) {
              const isSelected = state.avatar.accessory === accessory;
              return React.createElement(TouchableOpacity, {
                key: accessory,
                style: [
                  styles.optionButton,
                  { backgroundColor: isSelected ? state.theme.colors.primary : state.theme.colors.border }
                ],
                onPress: function() {
                  handlers.updateAvatar(state, 'accessory', accessory);
                },
                componentId: 'avatar-accessory-option-' + index
              },
                React.createElement(Text, { 
                  style: { 
                    color: isSelected ? '#FFFFFF' : state.theme.colors.textSecondary,
                    fontWeight: isSelected ? 'bold' : 'normal'
                  },
                  componentId: 'avatar-accessory-text-' + index
                }, accessory.charAt(0).toUpperCase() + accessory.slice(1))
              );
            })
          )
        ),
        
        React.createElement(View, { 
          style: [styles.customizationCard, { backgroundColor: state.theme.colors.card }],
          componentId: 'avatar-nickname-card'
        },
          React.createElement(Text, { 
            style: [styles.sectionTitle, { color: state.theme.colors.textPrimary }],
            componentId: 'avatar-nickname-title'
          }, 'Nickname'),
          React.createElement(TextInput, {
            style: [styles.input, { color: state.theme.colors.textPrimary, borderColor: state.theme.colors.border }],
            placeholder: 'Enter your cute nickname...',
            placeholderTextColor: state.theme.colors.textSecondary,
            value: state.avatar.nickname,
            onChangeText: function(text) {
              handlers.updateAvatar(state, 'nickname', text);
            },
            componentId: 'avatar-nickname-input'
          })
        ),
        
        React.createElement(TouchableOpacity, {
          style: [styles.saveButton, { backgroundColor: state.theme.colors.primary }],
          onPress: function() {
            handlers.saveProfile(state);
          },
          componentId: 'avatar-save-button'
        },
          React.createElement(Text, { 
            style: styles.saveButtonText,
            componentId: 'avatar-save-button-text'
          }, '💾 Save Avatar')
        ),
        
        renderAgeConfirmationModal(
          state.showModal,
          function() { handlers.confirmAge(state); },
          function() { state.setShowModal(false); },
          state.theme
        )
      )
    );
  };
  // @end:AvatarScreen

  // @section:BuddiesScreen-state @depends:[ThemeContext]
  const useBuddiesScreenState = function() {
    const themeContext = useTheme();
    const theme = themeContext.theme;
    
    const buddiesState = useState([
      {
        id: '1',
        name: 'Luna 🌙',
        avatar: '🐱',
        lastMessage: 'Hey! How are you?',
        timestamp: '2 min ago',
        online: true
      },
      {
        id: '2',
        name: 'Spark ⚡',
        avatar: '🤖',
        lastMessage: 'Want to chat?',
        timestamp: '5 min ago',
        online: true
      },
      {
        id: '3',
        name: 'Melody 🎵',
        avatar: '🦄',
        lastMessage: 'That was fun!',
        timestamp: '1 hour ago',
        online: false
      }
    ]);
    const buddies = buddiesState[0];
    const setBuddies = buddiesState[1];
    
    const selectedBuddyState = useState(null);
    const selectedBuddy = selectedBuddyState[0];
    const setSelectedBuddy = selectedBuddyState[1];
    
    const showChatState = useState(false);
    const showChat = showChatState[0];
    const setShowChat = showChatState[1];
    
    return {
      theme: theme,
      buddies: buddies,
      setBuddies: setBuddies,
      selectedBuddy: selectedBuddy,
      setSelectedBuddy: setSelectedBuddy,
      showChat: showChat,
      setShowChat: setShowChat
    };
  };
  // @end:BuddiesScreen-state

  // @section:BuddiesScreen-handlers @depends:[BuddiesScreen-state]
  const buddiesScreenHandlers = {
    selectBuddy: function(state, buddy) {
      state.setSelectedBuddy(buddy);
      state.setShowChat(true);
    },
    
    findNewBuddy: function(state) {
      const newBuddies = [
        { name: 'Comet 🌟', avatar: '🐰', lastMessage: 'Nice to meet you!', online: true },
        { name: 'Pixel 💫', avatar: '🤖', lastMessage: 'Hello there!', online: true },
        { name: 'Flower 🌸', avatar: '🦋', lastMessage: 'Hi! Let\'s chat!', online: true }
      ];
      
      const randomBuddy = newBuddies[Math.floor(Math.random() * newBuddies.length)];
      randomBuddy.id = Date.now().toString();
      randomBuddy.timestamp = 'just now';
      
      state.setBuddies(function(prev) {
        return [randomBuddy].concat(prev);
      });
      
      Platform.OS === 'web' ? window.alert('Found new chat buddy: ' + randomBuddy.name) : Alert.alert('New Buddy!', 'Found new chat buddy: ' + randomBuddy.name);
    },
    
    blockBuddy: function(state, buddyId) {
      const confirmAction = function() {
        state.setBuddies(function(prev) {
          return prev.filter(function(b) { return b.id !== buddyId; });
        });
        Platform.OS === 'web' ? window.alert('Buddy blocked successfully') : Alert.alert('Blocked', 'Buddy blocked successfully');
      };
      
      if (Platform.OS === 'web') {
        if (window.confirm('Are you sure you want to block this buddy?')) {
          confirmAction();
        }
      } else {
        Alert.alert(
          'Block Buddy',
          'Are you sure you want to block this buddy?',
          [
            { text: 'Cancel', style: 'cancel' },
            { text: 'Block', onPress: confirmAction, style: 'destructive' }
          ]
        );
      }
    }
  };
  // @end:BuddiesScreen-handlers

  // @section:BuddiesScreen-ChatModal @depends:[styles]
  const renderChatModal = function(visible, buddy, onClose, theme) {
    const messagesState = useState([
      { id: '1', text: 'Hey! How are you?', sent: false, timestamp: '2:30 PM' },
      { id: '2', text: 'I\'m doing great! Thanks for asking 😊', sent: true, timestamp: '2:32 PM' },
      { id: '3', text: 'That\'s awesome! What are you up to?', sent: false, timestamp: '2:33 PM' }
    ]);
    const messages = messagesState[0];
    const setMessages = messagesState[1];
    
    const inputTextState = useState('');
    const inputText = inputTextState[0];
    const setInputText = inputTextState[1];
    
    const sendMessage = function() {
      if (!inputText.trim()) return;
      
      const newMessage = {
        id: Date.now().toString(),
        text: inputText,
        sent: true,
        timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
      };
      
      setMessages(function(prev) {
        return prev.concat([newMessage]);
      });
      setInputText('');
    };
    
    if (!buddy) return null;
    
    return React.createElement(Modal, {
      visible: visible,
      animationType: 'slide',
      onRequestClose: onClose
    },
      React.createElement(View, { 
        style: [styles.chatContainer, { backgroundColor: theme.colors.background }],
        componentId: 'chat-modal-container'
      },
        React.createElement(View, { 
          style: [styles.chatHeader, { backgroundColor: theme.colors.card, borderBottomColor: theme.colors.border }],
          componentId: 'chat-modal-header'
        },
          React.createElement(TouchableOpacity, {
            onPress: onClose,
            style: styles.chatBackButton,
            componentId: 'chat-back-button'
          },
            React.createElement(MaterialIcons, { name: 'arrow-back', size: 24, color: theme.colors.textPrimary })
          ),
          React.createElement(View, { 
            style: styles.chatBuddyInfo,
            componentId: 'chat-buddy-info'
          },
            React.createElement(Text, { 
              style: styles.chatBuddyAvatar,
              componentId: 'chat-buddy-avatar'
            }, buddy.avatar),
            React.createElement(View, { componentId: 'chat-buddy-details' },
              React.createElement(Text, { 
                style: [styles.chatBuddyName, { color: theme.colors.textPrimary }],
                componentId: 'chat-buddy-name'
              }, buddy.name),
              React.createElement(Text, { 
                style: [styles.chatBuddyStatus, { color: buddy.online ? theme.colors.success : theme.colors.textSecondary }],
                componentId: 'chat-buddy-status'
              }, buddy.online ? '🟢 Online' : '⚫ Offline')
            )
          ),
          React.createElement(TouchableOpacity, {
            onPress: function() {
              const confirmAction = function() {
                Platform.OS === 'web' ? window.alert('Buddy reported successfully') : Alert.alert('Reported', 'Buddy reported successfully');
              };
              
              if (Platform.OS === 'web') {
                if (window.confirm('Report this buddy for inappropriate behavior?')) {
                  confirmAction();
                }
              } else {
                Alert.alert(
                  'Report Buddy',
                  'Report this buddy for inappropriate behavior?',
                  [
                    { text: 'Cancel', style: 'cancel' },
                    { text: 'Report', onPress: confirmAction, style: 'destructive' }
                  ]
                );
              }
            },
            style: styles.chatReportButton,
            componentId: 'chat-report-button'
          },
            React.createElement(MaterialIcons, { name: 'flag', size: 20, color: theme.colors.error })
          )
        ),
        
        React.createElement(FlatList, {
          data: messages,
          keyExtractor: function(item) { return item.id; },
          style: styles.messagesList,
          contentContainerStyle: { paddingVertical: 16 },
          renderItem: function(params) {
            const item = params.item;
            return React.createElement(View, {
              style: [
                styles.messageContainer,
                item.sent ? styles.sentMessage : styles.receivedMessage
              ],
              componentId: 'message-container-' + item.id
            },
              React.createElement(View, { 
                style: [
                  styles.messageBubble,
                  { 
                    backgroundColor: item.sent ? theme.colors.primary : theme.colors.card,
                    borderColor: theme.colors.border
                  }
                ],
                componentId: 'message-bubble-' + item.id
              },
                React.createElement(Text, { 
                  style: [
                    styles.messageText,
                    { color: item.sent ? '#FFFFFF' : theme.colors.textPrimary }
                  ],
                  componentId: 'message-text-' + item.id
                }, item.text),
                React.createElement(Text, { 
                  style: [
                    styles.messageTime,
                    { color: item.sent ? 'rgba(255,255,255,0.7)' : theme.colors.textSecondary }
                  ],
                  componentId: 'message-time-' + item.id
                }, item.timestamp)
              )
            );
          }
        }),
        
        React.createElement(KeyboardAvoidingView, {
          behavior: Platform.OS === 'ios' ? 'padding' : (Platform.OS === 'web' ? undefined : 'height'),
          style: [styles.chatInputContainer, { backgroundColor: theme.colors.card, borderTopColor: theme.colors.border }],
          componentId: 'chat-input-container'
        },
          React.createElement(TextInput, {
            style: [styles.chatInput, { color: theme.colors.textPrimary, borderColor: theme.colors.border }],
            placeholder: 'Type a cute message...',
            placeholderTextColor: theme.colors.textSecondary,
            value: inputText,
            onChangeText: setInputText,
            multiline: true,
            componentId: 'chat-input'
          }),
          React.createElement(TouchableOpacity, {
            style: [styles.sendButton, { backgroundColor: theme.colors.primary }],
            onPress: sendMessage,
            componentId: 'chat-send-button'
          },
            React.createElement(MaterialIcons, { name: 'send', size: 20, color: '#FFFFFF' })
          )
        )
      )
    );
  };
  // @end:BuddiesScreen-ChatModal

  // @section:BuddiesScreen @depends:[BuddiesScreen-state,BuddiesScreen-handlers,BuddiesScreen-ChatModal,styles]
  const BuddiesScreen = function() {
    const state = useBuddiesScreenState();
    const handlers = buddiesScreenHandlers;
    
    return React.createElement(View, { 
      style: [styles.container, { backgroundColor: state.theme.colors.background }],
      componentId: 'buddies-screen'
    },
      React.createElement(View, { 
        style: styles.header,
        componentId: 'buddies-screen-header'
      },
        React.createElement(Text, { 
          style: [styles.headerTitle, { color: state.theme.colors.textPrimary }],
          componentId: 'buddies-screen-title'
        }, '💬 Chat Buddies'),
        React.createElement(TouchableOpacity, {
          style: [styles.findBuddyButton, { backgroundColor: state.theme.colors.accent }],
          onPress: function() {
            handlers.findNewBuddy(state);
          },
          componentId: 'find-buddy-button'
        },
          React.createElement(MaterialIcons, { name: 'person-add', size: 16, color: '#FFFFFF' }),
          React.createElement(Text, { 
            style: styles.findBuddyText,
            componentId: 'find-buddy-text'
          }, ' Find New Buddy')
        )
      ),
      
      React.createElement(FlatList, {
        data: state.buddies,
        keyExtractor: function(item) { return item.id; },
        contentContainerStyle: { paddingBottom: Platform.OS === 'web' ? 90 : 100 },
        renderItem: function(params) {
          const buddy = params.item;
          return React.createElement(TouchableOpacity, {
            style: [styles.buddyCard, { backgroundColor: state.theme.colors.card }],
            onPress: function() {
              handlers.selectBuddy(state, buddy);
            },
            componentId: 'buddy-card-' + buddy.id
          },
            React.createElement(View, { 
              style: styles.buddyInfo,
              componentId: 'buddy-info-' + buddy.id
            },
              React.createElement(Text, { 
                style: styles.buddyAvatar,
                componentId: 'buddy-avatar-' + buddy.id
              }, buddy.avatar),
              React.createElement(View, { 
                style: styles.buddyDetails,
                componentId: 'buddy-details-' + buddy.id
              },
                React.createElement(Text, { 
                  style: [styles.buddyName, { color: state.theme.colors.textPrimary }],
                  componentId: 'buddy-name-' + buddy.id
                }, buddy.name),
                React.createElement(Text, { 
                  style: [styles.buddyMessage, { color: state.theme.colors.textSecondary }],
                  componentId: 'buddy-message-' + buddy.id
                }, buddy.lastMessage),
                React.createElement(Text, { 
                  style: [styles.buddyTime, { color: state.theme.colors.textSecondary }],
                  componentId: 'buddy-time-' + buddy.id
                }, buddy.timestamp)
              ),
              React.createElement(View, { 
                style: styles.buddyActions,
                componentId: 'buddy-actions-' + buddy.id
              },
                React.createElement(View, { 
                  style: [styles.onlineIndicator, { backgroundColor: buddy.online ? state.theme.colors.success : state.theme.colors.textSecondary }],
                  componentId: 'buddy-online-' + buddy.id
                }),
                React.createElement(TouchableOpacity, {
                  style: styles.blockButton,
                  onPress: function() {
                    handlers.blockBuddy(state, buddy.id);
                  },
                  componentId: 'buddy-block-' + buddy.id
                },
                  React.createElement(MaterialIcons, { name: 'block', size: 18, color: state.theme.colors.error })
                )
              )
            )
          );
        }
      }),
      
      renderChatModal(
        state.showChat,
        state.selectedBuddy,
        function() { state.setShowChat(false); },
        state.theme
      )
    );
  };
  // @end:BuddiesScreen

  // @section:SettingsScreen-state @depends:[ThemeContext]
  const useSettingsScreenState = function() {
    const themeContext = useTheme();
    const theme = themeContext.theme;
    const darkMode = themeContext.darkMode;
    const toggleDarkMode = themeContext.toggleDarkMode;
    
    const notificationsState = useState(true);
    const notifications = notificationsState[0];
    const setNotifications = notificationsState[1];
    
    const safetyModeState = useState(true);
    const safetyMode = safetyModeState[0];
    const setSafetyMode = safetyModeState[1];
    
    return {
      theme: theme,
      darkMode: darkMode,
      toggleDarkMode: toggleDarkMode,
      notifications: notifications,
      setNotifications: setNotifications,
      safetyMode: safetyMode,
      setSafetyMode: setSafetyMode
    };
  };
  // @end:SettingsScreen-state

  // @section:SettingsScreen @depends:[SettingsScreen-state,styles]
  const SettingsScreen = function() {
    const state = useSettingsScreenState();
    
    return React.createElement(ScrollView, {
      style: [styles.container, { backgroundColor: state.theme.colors.background }],
      contentContainerStyle: { paddingBottom: Platform.OS === 'web' ? 90 : 100 },
      componentId: 'settings-screen'
    },
      React.createElement(View, { 
        style: styles.header,
        componentId: 'settings-screen-header'
      },
        React.createElement(Text, { 
          style: [styles.headerTitle, { color: state.theme.colors.textPrimary }],
          componentId: 'settings-screen-title'
        }, '⚙️ Settings'),
        React.createElement(Text, { 
          style: [styles.headerSubtitle, { color: state.theme.colors.textSecondary }],
          componentId: 'settings-screen-subtitle'
        }, 'Customize your ChatiAva experience')
      ),
      
      React.createElement(View, { 
        style: [styles.settingsCard, { backgroundColor: state.theme.colors.card }],
        componentId: 'settings-appearance-card'
      },
        React.createElement(Text, { 
          style: [styles.settingsCardTitle, { color: state.theme.colors.textPrimary }],
          componentId: 'settings-appearance-title'
        }, '🎨 Appearance'),
        
        React.createElement(View, { 
          style: styles.settingRow,
          componentId: 'settings-dark-mode-row'
        },
          React.createElement(View, { 
            style: styles.settingInfo,
            componentId: 'settings-dark-mode-info'
          },
            React.createElement(Text, { 
              style: [styles.settingLabel, { color: state.theme.colors.textPrimary }],
              componentId: 'settings-dark-mode-label'
            }, 'Dark Mode'),
            React.createElement(Text, { 
              style: [styles.settingDescription, { color: state.theme.colors.textSecondary }],
              componentId: 'settings-dark-mode-desc'
            }, 'Switch to dark theme')
          ),
          React.createElement(TouchableOpacity, {
            style: [styles.toggle, { backgroundColor: state.darkMode ? state.theme.colors.primary : state.theme.colors.border }],
            onPress: state.toggleDarkMode,
            componentId: 'settings-dark-mode-toggle'
          },
            React.createElement(View, { 
              style: [styles.toggleThumb, { transform: [{ translateX: state.darkMode ? 20 : 0 }] }],
              componentId: 'settings-dark-mode-thumb'
            })
          )
        )
      ),
      
      React.createElement(View, { 
        style: [styles.settingsCard, { backgroundColor: state.theme.colors.card }],
        componentId: 'settings-safety-card'
      },
        React.createElement(Text, { 
          style: [styles.settingsCardTitle, { color: state.theme.colors.textPrimary }],
          componentId: 'settings-safety-title'
        }, '🛡️ Safety & Privacy'),
        
        React.createElement(View, { 
          style: styles.settingRow,
          componentId: 'settings-notifications-row'
        },
          React.createElement(View, { 
            style: styles.settingInfo,
            componentId: 'settings-notifications-info'
          },
            React.createElement(Text, { 
              style: [styles.settingLabel, { color: state.theme.colors.textPrimary }],
              componentId: 'settings-notifications-label'
            }, 'Notifications'),
            React.createElement(Text, { 
              style: [styles.settingDescription, { color: state.theme.colors.textSecondary }],
              componentId: 'settings-notifications-desc'
            }, 'Get notified of new messages')
          ),
          React.createElement(TouchableOpacity, {
            style: [styles.toggle, { backgroundColor: state.notifications ? state.theme.colors.primary : state.theme.colors.border }],
            onPress: function() { state.setNotifications(function(prev) { return !prev; }); },
            componentId: 'settings-notifications-toggle'
          },
            React.createElement(View, { 
              style: [styles.toggleThumb, { transform: [{ translateX: state.notifications ? 20 : 0 }] }],
              componentId: 'settings-notifications-thumb'
            })
          )
        ),
        
        React.createElement(View, { 
          style: styles.settingRow,
          componentId: 'settings-safety-mode-row'
        },
          React.createElement(View, { 
            style: styles.settingInfo,
            componentId: 'settings-safety-mode-info'
          },
            React.createElement(Text, { 
              style: [styles.settingLabel, { color: state.theme.colors.textPrimary }],
              componentId: 'settings-safety-mode-label'
            }, 'Safety Mode'),
            React.createElement(Text, { 
              style: [styles.settingDescription, { color: state.theme.colors.textSecondary }],
              componentId: 'settings-safety-mode-desc'
            }, 'Filter inappropriate content')
          ),
          React.createElement(TouchableOpacity, {
            style: [styles.toggle, { backgroundColor: state.safetyMode ? state.theme.colors.primary : state.theme.colors.border }],
            onPress: function() { state.setSafetyMode(function(prev) { return !prev; }); },
            componentId: 'settings-safety-mode-toggle'
          },
            React.createElement(View, { 
              style: [styles.toggleThumb, { transform: [{ translateX: state.safetyMode ? 20 : 0 }] }],
              componentId: 'settings-safety-mode-thumb'
            })
          )
        )
      ),
      
      React.createElement(View, { 
        style: [styles.settingsCard, { backgroundColor: state.theme.colors.card }],
        componentId: 'settings-help-card'
      },
        React.createElement(Text, { 
          style: [styles.settingsCardTitle, { color: state.theme.colors.textPrimary }],
          componentId: 'settings-help-title'
        }, '📞 Help & Support'),
        
        React.createElement(TouchableOpacity, {
          style: styles.helpButton,
          onPress: function() {
            Platform.OS === 'web' ? window.alert('Help Center coming soon!') : Alert.alert('Help', 'Help Center coming soon!');
          },
          componentId: 'settings-help-center-button'
        },
          React.createElement(Text, { 
            style: [styles.helpButtonText, { color: state.theme.colors.textPrimary }],
            componentId: 'settings-help-center-text'
          }, '💡 Help Center')
        ),
        
        React.createElement(TouchableOpacity, {
          style: styles.helpButton,
          onPress: function() {
            Platform.OS === 'web' ? window.alert('Report a problem - we\'ll investigate immediately!') : Alert.alert('Report', 'Report a problem - we\'ll investigate immediately!');
          },
          componentId: 'settings-report-problem-button'
        },
          React.createElement(Text, { 
            style: [styles.helpButtonText, { color: state.theme.colors.textPrimary }],
            componentId: 'settings-report-problem-text'
          }, '🚨 Report a Problem')
        ),
        
        React.createElement(TouchableOpacity, {
          style: styles.helpButton,
          onPress: function() {
            Platform.OS === 'web' ? window.alert('ChatiAva v1.0 - Made with ❤️ for safe, cute conversations!') : Alert.alert('About', 'ChatiAva v1.0 - Made with ❤️ for safe, cute conversations!');
          },
          componentId: 'settings-about-button'
        },
          React.createElement(Text, { 
            style: [styles.helpButtonText, { color: state.theme.colors.textPrimary }],
            componentId: 'settings-about-text'
          }, 'ℹ️ About ChatiAva')
        )
      )
    );
  };
  // @end:SettingsScreen

  // @section:TabNavigator @depends:[AvatarScreen,BuddiesScreen,SettingsScreen,navigation-setup]
  const TabNavigator = function() {
    const themeContext = useTheme();
    const theme = themeContext.theme;
    
    return React.createElement(View, { 
      style: { flex: 1, width: '100%', height: '100%', overflow: 'hidden' },
      componentId: 'tab-navigator-container'
    },
      React.createElement(Tab.Navigator, {
        screenOptions: function(params) {
          return {
            headerShown: false,
            tabBarStyle: {
              position: 'absolute',
              bottom: 0,
              backgroundColor: theme.colors.card,
              borderTopColor: theme.colors.border,
              borderTopWidth: 1,
              height: Platform.OS === 'web' ? 80 : 90,
              paddingBottom: Platform.OS === 'web' ? 16 : 20,
              paddingTop: 12,
              borderRadius: 24,
              marginHorizontal: 16,
              marginBottom: Platform.OS === 'web' ? 16 : 20,
              shadowColor: '#000000',
              shadowOffset: { width: 0, height: -4 },
              shadowOpacity: 0.1,
              shadowRadius: 12,
              elevation: 8
            },
            tabBarLabelStyle: {
              fontSize: 12,
              fontWeight: '600',
              marginTop: 4
            },
            tabBarActiveTintColor: theme.colors.primary,
            tabBarInactiveTintColor: theme.colors.textSecondary
          };
        }
      },
        React.createElement(Tab.Screen, {
          name: 'Avatar',
          component: AvatarScreen,
          options: {
            tabBarIcon: function(params) {
              return React.createElement(MaterialIcons, {
                name: 'face',
                size: 24,
                color: params.color
              });
            },
            tabBarLabel: '✨ Avatar'
          }
        }),
        React.createElement(Tab.Screen, {
          name: 'Buddies',
          component: BuddiesScreen,
          options: {
            tabBarIcon: function(params) {
              return React.createElement(MaterialIcons, {
                name: 'chat',
                size: 24,
                color: params.color
              });
            },
            tabBarLabel: '💬 Chat'
          }
        }),
        React.createElement(Tab.Screen, {
          name: 'Settings',
          component: SettingsScreen,
          options: {
            tabBarIcon: function(params) {
              return React.createElement(MaterialIcons, {
                name: 'settings',
                size: 24,
                color: params.color
              });
            },
            tabBarLabel: '⚙️ Settings'
          }
        })
      )
    );
  };
  // @end:TabNavigator

  // @section:styles @depends:[theme]
  const styles = StyleSheet.create({
    container: {
      flex: 1,
      paddingHorizontal: 20
    },
    header: {
      paddingTop: Platform.OS === 'web' ? 20 : 60,
      paddingBottom: 24,
      alignItems: 'center'
    },
    headerTitle: {
      fontSize: 32,
      fontWeight: 'bold',
      marginBottom: 8
    },
    headerSubtitle: {
      fontSize: 16,
      textAlign: 'center',
      lineHeight: 22
    },
    
    avatarPreview: {
      borderRadius: 24,
      padding: 24,
      alignItems: 'center',
      marginBottom: 24,
      shadowColor: '#000000',
      shadowOffset: { width: 0, height: 4 },
      shadowOpacity: 0.15,
      shadowRadius: 12,
      elevation: 8
    },
    avatarDisplay: {
      width: 100,
      height: 100,
      borderRadius: 50,
      justifyContent: 'center',
      alignItems: 'center',
      marginBottom: 16
    },
    avatarEmoji: {
      fontSize: 48
    },
    avatarName: {
      fontSize: 20,
      fontWeight: 'bold'
    },
    
    customizationCard: {
      borderRadius: 20,
      padding: 20,
      marginBottom: 16,
      shadowColor: '#000000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 8,
      elevation: 4
    },
    sectionTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 12
    },
    optionsRow: {
      flexDirection: 'row',
      flexWrap: 'wrap',
      gap: 8
    },
    optionButton: {
      paddingVertical: 8,
      paddingHorizontal: 16,
      borderRadius: 20,
      marginRight: 8,
      marginBottom: 8
    },
    colorOption: {
      width: 40,
      height: 40,
      borderRadius: 20,
      marginRight: 8,
      marginBottom: 8
    },
    
    input: {
      borderWidth: 2,
      borderRadius: 12,
      paddingVertical: 12,
      paddingHorizontal: 16,
      fontSize: 16,
      marginTop: 8
    },
    
    saveButton: {
      borderRadius: 20,
      paddingVertical: 16,
      alignItems: 'center',
      marginTop: 24,
      marginBottom: 32,
      shadowColor: '#000000',
      shadowOffset: { width: 0, height: 4 },
      shadowOpacity: 0.2,
      shadowRadius: 8,
      elevation: 6
    },
    saveButtonText: {
      color: '#FFFFFF',
      fontSize: 18,
      fontWeight: 'bold'
    },
    
    modalOverlay: {
      flex: 1,
      backgroundColor: 'rgba(0,0,0,0.5)',
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20
    },
    modalContent: {
      borderRadius: 24,
      padding: 24,
      width: '100%',
      maxWidth: 400,
      alignItems: 'center'
    },
    modalTitle: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 16,
      textAlign: 'center'
    },
    modalText: {
      fontSize: 16,
      textAlign: 'center',
      lineHeight: 22,
      marginBottom: 24
    },
    modalButtons: {
      flexDirection: 'row',
      gap: 12,
      width: '100%'
    },
    modalButton: {
      flex: 1,
      paddingVertical: 12,
      paddingHorizontal: 16,
      borderRadius: 20,
      alignItems: 'center'
    },
    
    findBuddyButton: {
      flexDirection: 'row',
      alignItems: 'center',
      paddingVertical: 8,
      paddingHorizontal: 12,
      borderRadius: 16,
      marginTop: 8
    },
    findBuddyText: {
      color: '#FFFFFF',
      fontSize: 14,
      fontWeight: 'bold'
    },
    
    buddyCard: {
      borderRadius: 20,
      padding: 16,
      marginHorizontal: 20,
      marginVertical: 8,
      shadowColor: '#000000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 8,
      elevation: 4
    },
    buddyInfo: {
      flexDirection: 'row',
      alignItems: 'center'
    },
    buddyAvatar: {
      fontSize: 32,
      marginRight: 12
    },
    buddyDetails: {
      flex: 1
    },
    buddyName: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 4
    },
    buddyMessage: {
      fontSize: 14,
      marginBottom: 2
    },
    buddyTime: {
      fontSize: 12
    },
    buddyActions: {
      alignItems: 'center',
      gap: 8
    },
    onlineIndicator: {
      width: 12,
      height: 12,
      borderRadius: 6
    },
    blockButton: {
      padding: 4
    },
    
    chatContainer: {
      flex: 1
    },
    chatHeader: {
      flexDirection: 'row',
      alignItems: 'center',
      paddingHorizontal: 16,
      paddingTop: Platform.OS === 'web' ? 16 : 44,
      paddingBottom: 16,
      borderBottomWidth: 1
    },
    chatBackButton: {
      marginRight: 12,
      padding: 4
    },
    chatBuddyInfo: {
      flex: 1,
      flexDirection: 'row',
      alignItems: 'center'
    },
    chatBuddyAvatar: {
      fontSize: 24,
      marginRight: 8
    },
    chatBuddyName: {
      fontSize: 18,
      fontWeight: 'bold'
    },
    chatBuddyStatus: {
      fontSize: 14
    },
    chatReportButton: {
      padding: 8
    },
    
    messagesList: {
      flex: 1,
      paddingHorizontal: 16
    },
    messageContainer: {
      marginVertical: 4
    },
    sentMessage: {
      alignItems: 'flex-end'
    },
    receivedMessage: {
      alignItems: 'flex-start'
    },
    messageBubble: {
      maxWidth: '80%',
      borderRadius: 18,
      paddingVertical: 10,
      paddingHorizontal: 14,
      borderWidth: 1
    },
    messageText: {
      fontSize: 16,
      lineHeight: 20
    },
    messageTime: {
      fontSize: 12,
      marginTop: 4
    },
    
    chatInputContainer: {
      flexDirection: 'row',
      alignItems: 'flex-end',
      paddingHorizontal: 16,
      paddingVertical: 12,
      borderTopWidth: 1
    },
    chatInput: {
      flex: 1,
      borderWidth: 1,
      borderRadius: 20,
      paddingVertical: 10,
      paddingHorizontal: 16,
      fontSize: 16,
      maxHeight: 100,
      marginRight: 8
    },
    sendButton: {
      width: 44,
      height: 44,
      borderRadius: 22,
      justifyContent: 'center',
      alignItems: 'center'
    },
    
    settingsCard: {
      borderRadius: 20,
      padding: 20,
      marginHorizontal: 20,
      marginVertical: 8,
      shadowColor: '#000000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 8,
      elevation: 4
    },
    settingsCardTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 16
    },
    settingRow: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'space-between',
      marginVertical: 8
    },
    settingInfo: {
      flex: 1
    },
    settingLabel: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 4
    },
    settingDescription: {
      fontSize: 14,
      lineHeight: 18
    },
    toggle: {
      width: 50,
      height: 30,
      borderRadius: 15,
      justifyContent: 'center',
      paddingHorizontal: 4
    },
    toggleThumb: {
      width: 22,
      height: 22,
      borderRadius: 11,
      backgroundColor: '#FFFFFF'
    },
    
    helpButton: {
      paddingVertical: 12,
      marginVertical: 4
    },
    helpButtonText: {
      fontSize: 16,
      fontWeight: '500'
    }
  });
  // @end:styles

  // @section:return @depends:[ThemeProvider,TabNavigator]
  return React.createElement(ThemeProvider, null,
    React.createElement(View, { style: { flex: 1, width: '100%', height: '100%' } },
      React.createElement(StatusBar, { barStyle: 'dark-content' }),
      React.createElement(TabNavigator)
    )
  );
  // @end:return
};
return ComponentFunction;
