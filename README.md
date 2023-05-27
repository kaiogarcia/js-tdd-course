import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createStackNavigator } from '@react-navigation/stack';
import { FontAwesome } from '@expo/vector-icons';
import UserList from './components/UserList';
import FavoritesButton from './components/FavoritesButton';

const Tab = createBottomTabNavigator();
const Stack = createStackNavigator();

const HomeScreen = () => (
  <Stack.Navigator>
    <Stack.Screen
      name="UserList"
      component={UserList}
      options={{
        headerStyle: {
          backgroundColor: '#040404',
        },
        headerTintColor: '#FDFDFD',
        headerTitleAlign: 'center',
        headerTitleStyle: {
          fontWeight: 'bold',
        },
      }}
    />
  </Stack.Navigator>
);

const FavoritesScreen = () => (
  <Stack.Navigator>
    <Stack.Screen
      name="FavoritesButton"
      component={FavoritesButton}
      options={{
        headerStyle: {
          backgroundColor: '#040404',
        },
        headerTintColor: '#FDFDFD',
        headerTitleAlign: 'center',
        headerTitleStyle: {
          fontWeight: 'bold',
        },
      }}
    />
  </Stack.Navigator>
);

const App = () => (
  <NavigationContainer>
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ color, size }) => {
          let iconName;
          if (route.name === 'Home') {
            iconName = 'home';
          } else if (route.name === 'Favorites') {
            iconName = 'heart';
          }
          return <FontAwesome name={iconName} size={size} color={color} />;
        },
      })}
      tabBarOptions={{
        activeTintColor: '#040404',
        inactiveTintColor: '#D6D6D6',
        style: {
          backgroundColor: '#F5F5F5',
        },
      }}
    >
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Favorites" component={FavoritesScreen} />
    </Tab.Navigator>
  </NavigationContainer>
);

export default App;
